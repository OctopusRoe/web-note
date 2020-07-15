## Goroutine和channel的高级运用

---

### worker pool (goroutine池)

在工作中我们通常会使用可以指定启动的 goroutine 数量 `worker pool` 模式，来控制 goroutine 的数量，防止 goroutine 泄露和暴涨

```go
func worker(id int,jobs <-chan int,results chan<- int) {
    for j := range jobs{
        fmt.Printf("worker:%d start job:%d\n", id,j)
        time.sleep(time.Second)
        fmt.Printf("worker:%d end job:%d\n",id,j)
        results <- j * 2
    }
}

func main() {
    jobs := make(chan int,100)
    results := make(chan int,100)
    
    // 开启3个 goroutine
    for w := 1; W < 3; W++ {
        go worker(w,jobs,results)
    }

    // 5个任务
    for j := 1; j < 5; j++{
        jobs <- j
    }

    close(jobs)

    // 输出结果
    for a := 1; a <= 5; a++ {
        <-results
    }
}
```

### select多路复用

在某些厂家下余姚同时从多个 `channel` 中接收数据，`channel` 在接收数据时，如果没有数据可以接收将会发生阻塞

`select` 的使用类似于 `switch` 语句，有一系列 `case` 分支和一个 `default` 分支，每个 `case` 会对应一个 `channel` 的通信(接收或发送)过程，`select` 会一直等待，直到某个 `case` 的通信操作完成时，就会执行 `case` 分支对应的语句

```go
select {
    case <- ch1:
        ...
    case data := <- ch2:
        ...
    case ch3 <- data:
        ...
    default:
        ...
}
```

```go
func main() {
    ch := make(chan int,1)
    for i := 0; i < 10; i++ {
        select{
            case x := <-ch:
                fmt.Println(x)
            case ch <- i:
        }
    }
}
```

使用 `select` 语句可以提高代码的可读性

1. 可以处理一个或多个 `channel` 的发送/接收操作
2. 如果多个 `select` 同时瞒住，`select` 会随机选择一个
3. 对于没有 `case` 的 `select{}` 会一直等待，可以用于阻塞 `main函数`

### 并发安全和锁

有时候在Go代码中可能会存在多个 `goroutine` 同时操作一个资源(临界区)，这种情况会发生 **竞态问题** (数据竞态)

#### 互斥锁

互斥锁是一种常用的控制共享资源访问的方法，它能够保证同时只有一个 `goroutine` 可以访问共享资源，Go语言中使用 `sync` 包的 `Mutex` 类型来实现互斥锁

```go
var x int64
var wg sync.WaitGroup
var lock sync.Mutex

func add() {
    for i := 0; i < 500; i++ {
        lock.Lock()     // 加锁
        x += 1
        lock.Unlock()   // 解锁
    }
    wg.Done()
}

func main() {
    wg.Add(2)
    go add()
    go add()
    wg.Wait()
    fmt.Println(x)
}
```

使用互斥锁能够保证同一时间内有且只有一个 `gproutine` 进入临界区，其他的 `goroutine` 则在等待锁，当互斥锁释放后，等待的 `goroutine` 才可以获取锁进入临界区，多个 `goroutine` 同时等待一个锁时，唤醒的策略是随机的

#### 读写互斥锁

互斥锁是完全互斥的，但是有很多实际的场景下是读多写少，当需要并发的去读取一个资源不涉及资源修改的时候是没有必要加锁的，这种场景下使用读写锁时更好的一种选择，读写锁在Go语言中使用 `sync` 包中的 `RWMutex` 类型

读写锁分为两种：

1. 读锁: 当一个 `goroutine` 获取读锁之后，其他的 `goroutine` 如果是获取读锁会继续获得锁，如果获取写锁就会等待
2. 写锁: 当一个 `goroutine` 获取写锁之后，其他的 `goroutine` 无论是获取读锁还是写锁都会等待

```go
var (
    x int64
    wg sync.WaitGroup
    lock sync.Mutex
    rwlock sync.RWMutex
)

func write() {
    // lock.Lock()      // 加互斥锁
    rwlock.Lock()       // 加写锁

    x += 1

    // 假设读操作耗时10毫秒
    time.Sleep(time.Millisecond * 10)

    rwlock.Unlock()     // 解写锁
    // lock.Unlock()    // 解互斥锁
    wg.Done()
}

func read() {
    // lock.Lock()      // 加互斥锁
    rwlock.RLock()      // 加读锁

    // 假设读操作耗时1毫秒
    time.Sleep(time.MillIsecond)

    rwlock.RUnlock()    // 解读锁
    // lock.Unlock()    // 解互斥锁
    wg.Done()
}

func main() {
    start := time.Now()
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go write()
    }

    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go read()
    }

    wg.Wait()
    end := time.Now()
    fmt.Println(end.Sub(start))
}
```

**注意：** 读写锁非常社会读多写少的场景，如果读和写的操作差别不大，读写锁的优势就发挥不出来

### sync.WaitGroup

在代码中为了等待而使用 `time.Sleep()` 是不优雅的，Go语言中可以使用 `sync.WaitGroup` 来实现并发任务的同步

方法名 | 功能 |
-|-|
(wg *WaitGroup) Add(delta int) | 计数器，需要启动几个 `goroutine` 填入数字 |
(wg *WaitGroup) Done() | 计数器 -1 |
(wg *WaitGroup) Wait() | 阻塞直到计数器变为0 |

`sync.WaitGroup` 内部维护着一个计数器，计数器的值可以增加和减少，如当启动 N 个 `goroutine` 时，就将计数器增加 N，每个任务完成时通过调用 `Done()` 方法将计数器 -1，而通过调用 `Wait()` 方法来等待 `goroutine` 任务完成，当计数器的值为0时，表示所有并发任务以完成

```go
var wg sync.WaitGroup

func hello() {
    defer wg.Done()
    fmt.Println("hello")
}

func main() {
    wg.Add(1)
    go hello()      // 启动另一个 goroutine 执行 hello()

    fmt.Println("main goroutine done")

    wg.Wait()
}
```

**注意：** `sync.WaitGroup` 是一个结构体，传递的时候要传递指针

### sync.Once

在编程的很多场景下需要确保某些操作在高并发的场景下只执行一次，如：只加载一次配置文件、只关闭一次通道等

Go语言中的 `sync` 包中提供了一个针对只执行一次场景的解决方案 `sync.Once`

```go
func (o *Once) Do(f func()) {}
```

**注意：** 如果要执行的函数 `f()` 需要传递参数就需要搭配闭包来使用

#### 加载配置文件

延迟一个开销很大的初始化操作到真正用到它的时候在执行时一个很好的实践，因为预先初始化一个变量(如在init函数中完成初始化)会增加程序启动耗时，而且有可能实际执行过程中这个变量没有用上，那么这个初始化操作就不是必须要做的

```go
var icons map[string]image.Image

func loadIcons() {
    icons  = map[string]image.Image{
        "left": loadIcon("left.png")
        "up": loadIcon("up.png")
        "right": loadIcon("right.png")
        "down": loadIcon("down.png")
    }
}

// Icon 被多个 goroutine 调用时不是并发安全的
func Icon(name string)image.Image {
    if icons == nil {
        LoadIcons()
    }
    return icons[name]
}
```

多个 `goroutine` 并发调用 Icon函数 时不是并发安全，现代的编译器和 CPU 可能会在保证每个 `goroutien` 都满足串行一致的基础上自由地重排访问内存的顺序，loadIcons函数可能会被重排位一下结果

```go
func loadIcons() {
	icons = make(map[string]image.Image)
	icons["left"] = loadIcon("left.png")
	icons["up"] = loadIcon("up.png")
	icons["right"] = loadIcon("right.png")
	icons["down"] = loadIcon("down.png")
}
```

在这种情况下就会出现即使判断了 `icons` 不是 nil 也不意味着变量初始化完成了，考虑到这种情况，能想到的办法就是添加互斥锁，来保证初始化 `icons` 的时候不会被其他的 `goroutine` 操作，但是这样又会引发性能问题

使用 `sync.Once` 改造的代码

```go
var icons map[string]image.Image

var loadIconsOnce sync.Once

func loadIcons() {
	icons = map[string]image.Image{
		"left":  loadIcon("left.png"),
		"up":    loadIcon("up.png"),
		"right": loadIcon("right.png"),
		"down":  loadIcon("down.png"),
	}
}

// Icon 是并发安全的
func Icon(name string) image.Image {
	loadIconsOnce.Do(loadIcons)
	return icons[name]
}
```

#### 并发安全的单列模式

```go
package singleton

import "sync"

type singleton struct {}

var instance *singleton
var once sync.Once

func GetInstance() *singleton {
    once.Do(func() {
        instance = &singleton{}
    })
    return instance
}
```

`sync.Once` 其实内部包含一个互斥锁和一个布尔值，互斥锁保证布尔值和数据的安全，而布尔值用来记录初始化是否完成，这样的设计就能保证促使和操作的时候是并发安全的并且初始化操作也不会被执行多次

#### sync.Map

Go语言中内置的 `map` 不是并发安全的

```go
var m = make(map[string]int)

func get(key string) int {
    return m[key]
}

func set(key string, value int) {
    m[key] = value
}

func main() {
    wg := sync.WaitGroup{}
    for i := 0; i < 20; i++ {
        wg.Add(1)
        go func(n int) {
            defer wg.Done()
            key := strconv.Itoa(n)
            set(key, n)
            fmt.Printf("key=%v,value=%v\n",key,get(key))
        }(i)
    }
    wg.Wait()
}
```

上面的例子，在运行20个 `goroutine` 时是不会报错的，但是 `goroutine` 数量超过20个代码运行会报 `fatal error: concurrent map writes` 错误，在这样的场景下，就需要为 `map` 加锁来保证并发的安全性了

Go语言 `sync` 包中就提供了一个 **开箱即用** 的并发安全版 `sync.Map`，不用像内置 `map` 一样使用 `make` 函数初始化就能直接使用

在 `sync.Map` 中无法使用内置 `map` 原生的方法，但是 `sync.Map` 中提供 `Store` `Load` `LoadOrStore` `Delete` `Range` 等操作方法

```go
var m = sync.Map{}

func main() {
    wg := sync.WaitGroup{}
    for i := 0; i < 20; i++ {
        wg.Add(1)
        go func (n int) {
            defer wg.Done()
            key := strconv.Itoa(n)
            m.Store(key, n)
            value, _ := m.Load(key)
            fmt.Printf("key=%v,value=%v\n"key,value)
        }(i)
    }
    wg.Wait()
}
```

### 原子操作

代码中的加锁操作因涉及内核态的上下文切换会比较耗时，针对基本数据类型，还可以使用原子操作来保证并发安全

原子操作是Go语言提供的方法，在用户态就可以完成，因此性能比加锁操作更好

Go语言中原子操作由内置的标准库 `sync/atomic` 提供

**注意：** `sync/atomic` 包中的方法查看 https://studygolang.com/pkgdoc

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
	"time"
)

type Counter interface {
	Inc()
	Load() int64
}

// 普通版
type CommonCounter struct {
	counter int64
}

func (c CommonCounter) Inc() {
	c.counter++
}

func (c CommonCounter) Load() int64 {
	return c.counter
}

// 互斥锁版
type MutexCounter struct {
	counter int64
	lock    sync.Mutex
}

func (m *MutexCounter) Inc() {
	m.lock.Lock()
	defer m.lock.Unlock()
	m.counter++
}

func (m *MutexCounter) Load() int64 {
	m.lock.Lock()
	defer m.lock.Unlock()
	return m.counter
}

// 原子操作版
type AtomicCounter struct {
	counter int64
}

func (a *AtomicCounter) Inc() {
	atomic.AddInt64(&a.counter, 1)
}

func (a *AtomicCounter) Load() int64 {
	return atomic.LoadInt64(&a.counter)
}

func test(c Counter) {
	var wg sync.WaitGroup
	start := time.Now()
	for i := 0; i < 1000; i++ {
		wg.Add(1)
		go func() {
			c.Inc()
			wg.Done()
		}()
	}
	wg.Wait()
	end := time.Now()
	fmt.Println(c.Load(), end.Sub(start))
}

func main() {
	c1 := CommonCounter{} // 非并发安全
	test(c1)
	c2 := MutexCounter{}  // 使用互斥锁实现并发安全
	test(&c2)
	c3 := AtomicCounter{} // 并发安全且比互斥锁效率更高
	test(&c3)
}
```

`sync/atomic` 包提供了底层的原子级内存操作，对于同步算法的实现很有用

这些函数必须谨慎的宝座正确使用，除了某些特殊的底层应用，使用通道或者 `sync` 包的函数或者类型实现同步更好