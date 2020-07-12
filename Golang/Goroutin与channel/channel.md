## Channel

---

虽然可以使用共享内存进行数据交换，但是共享内存在不同 `goroutine` 中容易发生竞态问题，为了保证数据交换的准确性，必须使用互斥量对内存进行加锁，这种做法势必照成性能问题

Go语言的并发模型是 `CSP (Communicating Sequential Processes)`，提倡 **通过通信共享内存** 而不是 **通过共享内存而实现通信**

如果说 `goroutine` 是Go程序并发的执行体，`channel` 就是它们之间的链接，`channel` 是可以让一个 `goroutine` 发送特定值到另一个 `goroutine` 的通信机制

Go语言中的 `channel` 是一种特殊的类型，通道像一个传送带或者队列，总是遵循先入先出 (First In First Out) 的规则，保证收发数据的顺序，每一个 `channel` 都是一个具体类型的导管，也就是声明 `channel` 的时候需要为其指定元素类型

### channel 类型

`channel` 是一种类型，一种引用类型

```go
var 变量名 chan 元素类型
```

```go
var a chan int      // 声明一个传递 int 的通道
var b chan string   // 声明一个传递 string 的通道
var c chan bool     // 声明一个传递 bool 的通道
var d chan []int    // 声明一个传递 []int 的通道
```

### 创建 channel

通道是引用类型，通道类型的空值为 `nil`

```go
var a chan int
fmt.Println(a)      // <nil>
```

引用类型需要使用 `make()` 函数初始化之后才能使用

```go
make(chan 元素类型 [,缓存大小])
```

```go
a := make(chan int)
b := make(chan int,1)
c := make(chan int,100)
```

### channel操作

通道有 `send(发送)` `receive(接收)` `close(关闭)` 三种操作

发送和接收都使用 `<-` 符号

```go
/* 定义一个通道 */
a := make(chan int ,1)

/* 发送 */
a <- 10         // 把 10 发送到 a 通道中

/* 接收 */
x := <- a       // 从 a通道中接收值并赋值给变量x
<- a            // 从 a 通道中接收值，忽略结果

/* 关闭通道 */
close(a)
```

关于关闭通道需要注意的事情是，只有在通知接收方 `goroutine` 所有数据都发送完毕的时候才需要关闭通道，通道是可以被垃圾回收机制回收的，它和关闭文件是不一样的，在结束操作之后关闭文件时必须要做的，但关闭通道不是必须的

关闭后的通道有以下特点：

1. 对一个关闭的通道在发送值就会导致 `panic`
2. 对一个关闭的通道进行接收会一直获取值直到通道为空
3. 对一个关闭的并且没有值的通道执行接收操作会得到通道中元素的对应类型的零值
4. 关闭一个已经关闭的通道会导致 `panic`

### 无缓冲的通道

无缓冲的通道又称为 **阻塞** 的通道

```go
func mian() {
    a := make(chan int)
    a <- 10
    fmt.Println("success")
}
```

上面的代码可以通过编译，但是在运行时会出现错误

```
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan send]:
main.main()
        .../src/github.com/Q1mi/studygo/day06/channel02/main.go:8 +0x54
```

这是因为使用 `a := make(chan int)` 创建的是无缓冲的通道，无缓冲的通道只有在有人接收值的时候才能发送值，简单来说就是，无缓冲的通道必须有接收才能发送

`a <- 10` 这一行代码因为无接收者，会形成 `deadlock`(死锁)

最简单的一个办法就是用 `goroutine` 去接收

```go
func a(c chan int) {
    test := <- c
    fmt.Println("success",test)
}

func main() {
    c := make(chan int)
    go a(c)
    c <- 10
    fmt.Println("send success")
}
```

无缓冲通道上的发送操作会阻塞，直到另一个 `groutine` 在该通道上执行接收操作，这时值才能发送成功，两个 `goroutine` 将继续执行

相反，如果接收操作先执行，接收方的 `goroutien` 将会阻塞，直到另一个 `goroutine` 在该通道上发送一个值

使用无缓冲通道进行通信将导致发送和接收的 `goroutine` 同步化，因此无缓冲通道也被称为 **同步通道**

### 有缓冲的通道

可以在使用 `make()` 函数初始化通道的时候为其指定通道的容量

```go
func main() {
    a := make(chan int,100) // 创建一个容量为100 的缓冲区通道
    a <- 10
    fmt.Println("send success")
}
```

只要通道的容量大于零，那么该通道就是有缓冲的通道，通道的容量表示通道中能存放元素的数量，如果通道中的元素数量超过容量，就会造成阻塞

可以使用 `len()` 函数获取通道内元素的数量，使用 `cap()` 函数获取通道的容量

### for range 从通道循环取值

当向通道中送完数据时，可以通过 `close()` 函数来关闭通道

当通道被关闭时，再往该通道发送值会引发 `panic` 错误，而从该通道取值的操作会先取完通道中的值，如果通道中的值被取完了，通道会返回该通道中元素对应类型的零值，和一个 `false`

```go
func main() {
    a := make(chan int)
    b := make(chan int)

    /* 开启一个 goroutine 并发送0-100到 a 通道中 */
    go func() {
        for i:=0;i<100;i++{
            a <- i
        }
        close(a)
    }()

    /* 开启一个 goroutine 从 a 通道中接收值，并将该值的平方发送到 b 通道中 */
    go func() {
        for {
            i,ok := <- a
            if !ok {
                break
            }
            b <- i * i
        }
        break
    }()

    /* 在 main 函数中的 goroutine 中从 b 中接收值并输出到控制台 */
    for i := range b {  // 通道关闭后会退出 for range 循环
        fmt.Println(i)
    }
}
```

上面的例子可以看到有两种方式在接收值的时候判断该通道是否被关闭，不过通常使用的是 `for range` 的方式，使用 `for range` 遍历通道，当通道被关闭的时候会退出 `for range`

### 单向通道

有时候会将通道作为参数在多个函数之间传递，在不同函数中使用通道都会对其进行限制，比如限制通道在函数中只能发送或只能接受

Go语言中提供了 **单向通道** 来处理这种情况

```go
/* 定义一个只能从通道接受值的函数 */
func counter(out chan<- int) {
    for i:=0;i<100;i++{
        out <- i
    }
    close(out)
}

/* 定义一个既能读通道中的值，又能往通道中写值的函数 */
func squarer(out chan<- int, in <-chan int) {
    for i := range in {
        out <- i *i
    }
    close(out)
}

/* 定义一个只能从通道中读值的函数 */
func printer(in <-chan int) {
    for i := range in {
        fmt.Println(i)
    }
}

func main() {
    a := make(chan int)
    b := make(chan int)
    go counter(a)
    go squarer(b,a)
    printer(b)
}
```

1. `chan <- int` 是一个只写单向通道 (只能对其写入 int 类型的值)，可以对其执行发送操作但是不能执行接收操作
2. `<-chan int` 是一个只读单向通道 (只能从其读取 int 类型值)，可以对其执行接收操作但是不能执行发送操作

在函数传参及任何赋值操作中可以将双向通道转换为单向通道，但是将单向通道转换为双向通道是不可以的

### channel总结

`channel` 常见的异常总结

channel | nil | 非空 | 空 | 满 | 没满 |
-|-|-|-|-|-|
**接收** | 阻塞 | 接收值 | 阻塞 | 接收值 | 接收值 |
**发送** | 阻塞 | 发送值 | 发送值 | 阻塞 | 发送值 |
**关闭** | panic | 关闭成功，读完数据后，返回零值 | 关闭成功，返回零值 | 关闭成功，读完数据后，返回零值 | 关闭成功，读完数据后，返回零值 |

关闭已经关闭的 `channel` 也会引发 `panic`