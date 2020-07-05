## Goroutine

---

### 并发与并行

并发：**同一时间段** 内执行多个任务

并行：**同一时刻** 执行多个任务

Go语言的并发通过 `goroutine` 实现，`goroutine` 类似于线程，属于 **用户态的线程**，可以根据需要创建成千上万个 `goroutine` 并发工作，`goroutine` 是由Go语言的运行时 `runtime` 来调度的，而线程则是由操作系统来调度的

Go语言还提供了 `channel` 在多个 `goroutine` 间进行通信

`goroutine` 和 `channel` 是Go语言秉承 CSP (Communicating Sequential Process) 并发模式的重要实现基础

#### goroutine

在 java/c++ 中药实现并发编程的时候，往往需要自己维护一个线程池，并且需要自己去包装一个又一个任务，同时需要自己去调度线程执行任务并维护上下文切换

而Go语言中的 `goroutine` 是一个只需要定义任务，让程序去自动的把任务分配到CPU上实现并发，`goroutine` 的概念类似于线程，但是 `goroutine` 是由Go的运行时 `runtime` 调度和管理的

Go程序会智能的将 `goroutine` 中的任务合理的分配给每个CPU，并且Go语言在它语言层面就已经内置了调度和上下文切换的机制

#### 使用 goroutine

Go语言中使用 `goroutine` 只需要在调用函数的时候在前面加上 `go` 关键字，就可以为一个函数创建一个 `goroutine`

一个 `goroutine` 必定对应一个函数，可以创建多个 `goroutine` 去执行相同的函数

#### 启动单个 gotoutine

```go
func hello() {
    fmt.Println("hello world goroutine")
}

func main() {
    /* 启动另一个 goroutine 执行 hello() 函数 */
    go hello()
    fmt.Println("main goroutine done")
}
```

在上面的例子中，程序只会输出 `main goroutine done` 而不会和往常一样输出 `hello world goroutine`，这是因为在程序启动时，Go程序就会为 `main()` 函数创建一个默认的 `goroutine`

而 `main()` 函数中的 `go hello()` 启动也是需要时间的，在 `main()` 函数返回的时候，`main()` 函数本身的 `goroutine` 就结束了，所有在 `main()` 函数中启动的 `goroutine` 也会一同结束

最简单的方法就是调用 `time.Sleep()` 方法让程序停一段时间，等待 `go hello()` 的返回

```go
func hello() {
    fmt.Println("hello world goroutine")
}

func main() {
    /* 启动另一个 goroutine 执行 hello() 函数 */
    go hello()
    fmt.Println("main goroutine done")
    /* 调用 time.Sleep() 方法让程序暂停 1s */
    time.Sleep(time.Second)
}
```

这样程序会先输出 `main goroutine done`，然后在输出 `hello world goroutine`

#### 启动多个 goroutine

在Go语言中实现并发就是这样，通过关键字 `go` 还可以启动多个 `goroutine` 来调用同一个函数

```go
var wg sync.WaitGroutp

func hello(i int) {
    defer wg.Done()     // goroutine结束就登记-1
    fmt.Println("hello world goroutine",i)
}

func main() {
    for i:=0;i<10;i++{
        wg.Add(1)       // goroutine启动就+1
        go hello(i)
    }
    wg.Wait()           // 等待所有登记过的 goroutine 都结束
}
```

多次执行后，会发现每次输出的顺序都不一致，这是因为 `goroutine` 是并发执行的，而且 `goroutine` 的调度是随机的

### goroutine与线程

在OS线程中一般都有固定的栈内存 (通常为2MB)，一个 `goroutien` 的栈在其生命周期开始时只有很小的栈 (典型情况下为2KB)，`goroutien` 的栈不是固定的，可以按需要增大和缩小，`goroutine` 的栈大小限制可以达到1GB

#### goroutien调度

`GPM` 是Go语言运行时 (runtime) 层面的实现，是Go语言实现的一套调度系统，区别于操作系统调度OS线程

1. G：`goroutine`，里面除了存放本 `goroutine` 信息外，还有与所在 P 的绑定等信息
2. P：管理着一组 `goroutine` 队列，P里面会存储当前 `goroutine` 运行的上下文环境 (函数指针，堆栈地址及地址边界)，P会对自己管理的 `goroutine` 队列做一些调度 (如：把占用CPU时间较长的 `goroutine` 暂停，运行后续的 `goroutine`) 当自己的队列完成就去全局队列里取，如果全局队列里也完成了会去其他P的队列里抢任务
3. M：是Go运行时 (runtime) 对操作系统内核线程的虚拟，M与内核线程一般是一一映射的关系，一个 `goroutine` 最终是要放到M上执行的

P 与 M 一般也是一一对应的，P 管理着一组 G 挂载在 M 上运行，当一个 G 长久阻塞在一个 M 上时，`runtime` 会新建一个 M，阻塞 G 所在的 P 会把其他的 G 挂载在新建的 M 上，当旧的 G 阻塞完成或认为其已经挂掉时，回收旧的 M

P 的个数是通过 `runtime.GOMAXPROCS()` 设定 (最大256)，Go1.5版本之后默认为物理线程数，通过 `runtime.NumCPU()` 查看

单从线程调度上讲，Go语言的优势在于OS线程是由OS内核来调度的，`goroutine` 则是由Go的运行时 (runtime) 来调度的，这个调度器使用一个称为 `m:n` 的调度技术 (复用/调度 m 个 `goroutine` 到 n 个OS线程中)，其一大特点就是 `goroutine` 的调度室在 **用户态** 下完成的，不用涉及到内核态与用户态之间的频繁切换，包括内存的分配与释放，都是在 **用户态** 维护着一块大的内存池，不直接调用系统的 malloc函数 (除非内存池需要改变)，使用成本比调度OS线程低很多，另一方面充分利用了多核的硬件资源，近似于把若干 `goroutine` 平均的分配在物理线程上，再加上本身 `goroutine` 的超轻量，以上的特点保证了Go语言调度方面的性能

#### GOMAXPROCS

Go运行时的调度器使用 `GOMAXPROCS` 参数来确定需要使用多少个OS线程来同时执行Go代码，默认值是机器上的CPU核心数 (GOMAXPROCS 是 m:n调度中的n)

可以通过设置 `runtime.GOMAXPROCS()` 方法来设置当前程序并发时占用的CPU逻辑核心数

```go
var wg sync.WaitGroutp

func a() {
    defer wg.Done()
    for i:=1;i<10;i++{
        fmt.Println('A',i)
    }
}

func b() {
    defer wg.Done()
    for i:=1;i<10;i++{
        fmt.Println('B',i)
    }
}


func main() {
    runtime.GOMAXPROCS(1)
    wg.Add(2)
    go a()
    go b()
    wg.Wait()
}
```

两个任务只有一个逻辑核心，此时是做完一个任务在做另一个任务

```go
var wg sync.WaitGroutp

func a() {
    defer wg.Done()
    for i:=1;i<10;i++{
        fmt.Println('A',i)
    }
}

func b() {
    defer wg.Done()
    for i:=1;i<10;i++{
        fmt.Println('B',i)
    }
}


func main() {
    runtime.GOMAXPROCS(2)
    wg.Add(2)
    go a()
    go b()
    wg.Wait()
}
```

这个则是两个任务分配2个逻辑核心

Go语言中的操作系统线程和 `goroutine` 的关系

1. 一个操作系统线程对应用户态多个 `goroutine`
2. Go程序可以同时使用多个操作系统线程
3. `goroutine` 和OS线程是多对多的关系，即 `m:n`