## Go语言实现通信

---

### TCP服务端

一个TCP服务端可以同时连接很多个客户端，因为Go语言中创建多个 `goroutine` 实现并发非常高效，所以可以每建立一次链接就创建一个 `goroutine` 去处理

TCP服务端程序的处理流程:

1. 监听端口
2. 接收客户端请求建立链接
3. 创建 `goroutine` 处理链接

Go语言内置的 `net` 包实现TCP服务端

```go
// TCP server

// 处理函数
func process(conn net.Conn) {
    defer conn.Close()      // 关闭链接
    for {
        reader := bufio.NewReader(conn)
        var buf [128]byte
        n, err := reader.Read(buf[:])   // 读取数据
        if err != nil {
            fmt.Println("read from client failed,err:",err)
            break
        }
        recvStr := string(buf[:n])
        fmt.Println("收到client端发来的数据：",recvStr)
        conn.Write([]byte(recvStr))     // 发送数据
    }
}

func main() {
    listen,err := net.Listen("tcp","127.0.0.1:20000")
    if err != nil {
        fmt.Println("listen failed,err:",err)
        break
    }
    for {
        conn, err := listen.Accept()    // 建立链接
        if err != nil {
            fmt.Println("listen failed,err:",err)
            continue
        }
        go process(conn)                // 启动一个 goroutine 处理链接
    }
}
```

编译后，执行文件，就是一个简单的服务器

### TCP客户端

一个TCP客户端进行TCP通信的流程如下:

1. 建立与服务端的链接
2. 进行数据收发
3. 关闭链接

使用Go语言内置的 `net` 包来实现TCP客户端

```go
// 客户端

func main() {
    conn, err := net.Dial("tcp","127.0.0.1:20000")
    if err != nil {
        fmt.Println("dial failed,err:",err)
        return
    }

    defer conn.Close()      // 关闭链接
    inputReader := bufio.NewReader(os.Stdin)
    for {
        input, _ := inputReader.ReadString('\n')    // 读取用户输入
        inputInfo := strings.Trim(input,"\r\n")
        if strings.ToUpper(inputInfo) == 'Q' {      // 如果输入 q 就返回
            return
        }
        _, err = conn.Write([]byte(inputInfo))      // 发送数据
        if err != nil {
            fmt.Println("write failed,err:",err)
            return
        }
        buf := [512]byte{}
        n, err := conn.Read(buf[:])
        if err != nil {
            fmt.Println("recv failed,err:",err)
            return
        }
        fmt.Println(string(buf[:n]))
    }
}
```

编译后，启动服务端，在启动客户端，在控制台输入内容 enter 键后，就能在 server端的控制台看见服务端返送的数据，这就是一个简单的TCP通信

### TCP黏包

TCP的socket编程中，发送端和接收端都有成对的socket，发送端为了将多个发往接收端的包，更加高效的发给接收端，采用了优化算法(Nagle算法)，将多次间隔较小，数量较少的数据，合并成一个数据量大的数据块，然后进行封包

TCP黏包就是指发送方发送的若干包数据到达接收方时黏成了一包，从接收缓冲区来看，后一包数据的头紧接着前一包数据的尾，出现黏包的原因是多方面的，可能是来自发送方，也可能来自接收方

1. 由 Nagle 算法造成的发送端黏包
2. 接收端接收不及时造成的接收端黏包

出现黏包的关键在于接收方不确定将要传输的数据包的大小，因此可以对数据包进行封包和拆包的操作

封包: 封包就是给一段数据加上包头，这样数据包就分为包头和包体两部分内容(过滤非法包时，封包会加入包尾内容)，包头部分的长度是固定的，其中储存了包体的长度

拆包: 根据包头长度固定以及包头中含有包体的长度变量就能正确的拆分出一个完整的数据包

```go
package proto

import (
    "bufio"
    "bytes"
    "encoding/binary"
)

// 封包函数
func Encode(message string)([]byte,error) {
    
    // 读取消息长度，转换成 int32类型(占用4byte)
    var length = int32(len(message))
    var pkg = new(bytes.Buffer)
    
    // 写入消息头，按照 little-endian 放入内存中
    err := binary.Write(pkg, binary.LittleEndian, length)
    if err != nil {
        return nil, err
    }
    
    // 写入消息实体，按照 little-endian 放入内存中
    err = binary.Write(pkg, binary.LittleEndian, []byte(message))
    if err != nil {
        return nil, err
    }
    return pkg.Bytes(), nil
}

// 解包函数
func Decode(reader *bufio.Reader) (string, error) {
    
    // 读取数据包的包头中的长度
    lengthByte, _ := reader.Peek(4)     // 读取前4 Byte 的数据
    lengthBuff := bytes.NewBuffer(lengthByte)
    var length int32
    err := binary.Read(lengthBuff, binary.LittleEndian, &length)
    if err != nil {
        return "", err
    }

    // buffered返回缓冲中现有的可读取的字节数
    if int32(reader.Buffered()) < length + 4 {
        return "",err
    }

    // 读取真正的消息数据
    pack := make([]byte, int(4 + length))
    _, err = reader.Read(pack)
    if err != nil {
        return "",err
    }
    return string(pack[4:]), nil
}
```

使用上面包中的方法在服务端和接收端处理数据就可以解决黏包问题

```go
// server

func process(conn net.Conn) {
    defer conn.Close()
    reader := bufio.NewReader(conn)
    for {
        msg, err := proto.Decode(reader)
        if err == io.EOf {
            return
        }
        if err != nil {
            fmt.Println("decode msg failed,err",err)
            return
        }
        fmt.Println("从客户端接收到的数据："msg)
    }
}

func main() {
    listen, err := net.Listen("tcp","127.0.0.1:30000")
    if err != nil {
        fmt.Println("listen failed,err:",err)
        return
    }
    defer listen.Close()
    for {
        conn, err := listen.Accept()
        if err != nil {
            fmt.Println("accept failed,err:",err)
            continue
        }
        go process(conn)
    }
}
```

```go
// 客户端
func mian() {
    conn,err := net.Dial("tcp","127.0.0.1:30000")
    if err != nil {
        fmt.Println("dial failed,err:",err)
        return
    }
    defer conn.Close()
    for i := 0; i < 20; i++ {
        msg := "Hello,Hello World"
        data, err := proto.Encode(msg)
        if err != nil {
            fmt.Println("encode failed,err:",err)
            return
        }
        conn.Write(data)
    }
}
```

### Go语言实现UDP通信

UDP协议(User Datagram Protocol) 用户数据报协议，是OSI参考模型中一种 **无连接** 的传输层协议，不需要建立链接就能直接进行数据发送和接收，属于不可靠的，没有时序的通信，但是相对于TCP协议来说，UDP协议的实现简单，占用资源少，实时性较好，常用于视频直播相关领域

```go
// UDP server

func main() {
    listen, err := net.ListenUDP("udp", &net.UDPAddr{
        IP: net.IPv4(127,0,0,1),
        Port: 30000,
    })
    if err := nil {
        fmt.Println("listen failed,err:",err)
        return
    }
    defer listen.Close()
    for {
        var data [1024]byte

        // 接收数据
        n, addr, err := listen.ReadFromUDP(data[:])
        if err != nil {
            fmt.Println("read UDP failed,err:",err)
            continue
        }
        fmt.Printf("data:%v addr:%v count:%v\n",string(data[:n]),addr,n)

        // 发送数据
        _, err = listen.WriteToUDP(data[:n],addr)
        if err != nil {
            fmt.Println("write UDP failed,err:",err)
            continue
        }
    }
}
```

```go
// UDP客户端

func main() {
    socket, err := net.DialUDP("udp",nil,&net.UDPAddr{
        IP: net.IPv4(127,0,0,1),
        Port: 30000,
    })
    if err != nil {
        fmt.Println("dial UDP failed,err:",err)
        return
    }
    defer socket.Close()

    senData := []byte("Hello server")

    // 发送数据
    _, err = socket.Write(senData)
    if err != nil {
        fmt.Println("write failed,err:",err)
        return
    }

    data := make([]byte,4096)
    
    // 接收数据
    n, remoteAddr, err := socket.ReadFromUDP(data)
    if err != nil {
        fmt.Println("read UDP failed,err:",err)
        return
    }

    fmt.Printf("recv:%v addr:%v count:%v\n",string(data[:n]),remoteAddr,n)
}
```