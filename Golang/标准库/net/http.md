## net/http

---

Go语言内置的 `net/http` 包提供了 HTTP 客户端和服务端的实现

### HTTP客户端

#### 基本的HTTP/HTTPS请求

Get、Head、Post和PostForm 函数发出 HTTP/HTTPS 请求

```go
res, err := http.Get("http://www.baidu.com")

res, err := http.Post("http://www.baidu.com", "image/jpeg", &buf)

res, err := http.PostForm(
    "http://www.baidu.com",
    url.Values{"key":{"value"},"id":{"123"}}
    )
```

程序在使用完 response 后必须关闭回复的主体

```go
res, err := http.Get("http://www.baidu.com")
if err != nil {
    ***
}
defer res.Body.Close()
body, err := ioutil.ReadAll(res.Body)
```

#### GET请求

```go
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    res, err := http.Get("http://www.baidu.com")
    if err != nil {
        fmt.Println("get failed,err:",err)
        return
    }
    defer res.Body.Close()
    body, err := ioutil.ReadAll(res.Body)
    if err != nil {
        fmt.Println("read from res.Body failed,err:",err)
        return
    }
    fmt.Print(string(body))
}
```

#### 带参数的GET请求

如果GET请求需要带参数，则需要内置的 `net/url` 包来处理

```go
func main() {
    apiUrl := "http://127.0.0.1:4000/get"

    // URL param
    data := url.Values{}
    data.Set("name","OctopusRoe")
    data.Set("age","18")
    u, err := url.ParseRequestURI(apiUrl)
    if err != nil {
        fmt.Println("parse url failed,err:",err)
        return
    }

    // URL encode 编码
    u.RawQuery = data.Encode()
    fmt.Println(u.String())
    res, err := http.Get(u.String())
    if err != nil {
        fmt.Println("post failed,err:",err)
        return
    }
    defer res.Body.Close()
    b, err := ioutil.ReadAll(res.Body)
    if err != nil {
        fmt.Println("get res failed,err:",err)
        return
    }
    fmt.Println(string(b))
}
```

Server 服务端

```go
// server

func getHandler(w http.ResponseWriter, r *http.Request) {
    defer r.Body.Close()
    data := r.URL.Query()
    fmt.Println(data.Get("name"))
    fmt.Println(data.Get("age"))
    answer := `{"status": "ok"}`
    w.Write([]byte(answer))
}
```

#### POST请求

```go
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
)

func main() {
    url := "http://127.0.0.1:4000"
    contentType := "application/json"
    data := `{"name":"OctopusRoe","age":18}`
    res, err := http.Post(url,contentType,strings.NewReader(data))
    if err != nil {
        fmt.Println("post failed,err:",err)
        return
    }
    defer res.Body.Close()
    b, err := ioutil.ReadAll(res.Body)
    if err != nil {
        fmt.Println("get res failed,err:",err)
        return
    }
    fmt.Println(string(b))
}
```

server 服务端

```go
func postHandler(w http.ResponseWriter, r *http.Request) {
	defer r.Body.Close()
	// 1. 请求类型是application/x-www-form-urlencoded时解析form数据
	r.ParseForm()
	fmt.Println(r.PostForm) // 打印form数据
	fmt.Println(r.PostForm.Get("name"), r.PostForm.Get("age"))
	// 2. 请求类型是application/json时从r.Body读取数据
	b, err := ioutil.ReadAll(r.Body)
	if err != nil {
		fmt.Printf("read request.Body failed, err:%v\n", err)
		return
	}
	fmt.Println(string(b))
	answer := `{"status": "ok"}`
	w.Write([]byte(answer))
}
```

#### 自定义Client

如果需要管理HTTP客户端的请求头、重定向策略和其他设置

```go
client := &http.Client{
    CheckRedirect: redirectPolicyFunc,
}
res, err := client.Get("http://www.baidu.com")

req, err := http.NewRequest("get","http://www.baidu.com",nil)

req.Header.Add("If-None-Match",`W/"wyzzy"`)

res, err := client.Do(req)
```

#### 自定义Transport

要管理代理、TLS配置、Keep-alive、压缩和其他设置

```go
tr := &http.Transport{
    TLSClientConfig: &tls.Config{RootCAs: pool},
    DisableCompression: true,
}

client := &http.Client{Transport: tr}

res, err := client.Get("https://www.baidu.com")
```

Client和Transport类型都可以安全的倍多个 `goroutine` 同时使用，出于效率考虑，应该一次建立，尽量重用

### server服务端

ListenAndServe使用指定的尖头地址和处理器启动一个HTTP服务端，处理器参数通常是nil，这表示采用包变量 DefaultServeMux 作为处理器

Handle和HandleFunc函数可以向DefaultServeMux添加处理器。

```go
http.Handle("/foo", fooHandler)
http.HandleFunc("/bar", func(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hello, %q", html.EscapeString(r.URL.Path))
})
log.Fatal(http.ListenAndServe(":8080", nil))
```

使用Go语言中的 `net/http` 包是对 `net` 包的进一步封装，专门用来处理HTTP协议数据

```go
func sayHello(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintln(w, "Hello OctopusRoe")
}

func main() {
    http.HandleFunc("/",sayHello)
    err := http.ListenAndServe(":4000",nil)
    if err != nil {
        fmt.Println("http server failed,err:",err)
        return
    }
}
```

编译执行后，可以在本机的 `127.0.0.1:4000` 上看到内容

#### 自定义Server

要管理服务端的行为，可以创建一个自定义的Server

```go
s := &http.Server{
	Addr:           ":8080",
	Handler:        myHandler,
	ReadTimeout:    10 * time.Second,
	WriteTimeout:   10 * time.Second,
	MaxHeaderBytes: 1 << 20,
}
log.Fatal(s.ListenAndServe())
```