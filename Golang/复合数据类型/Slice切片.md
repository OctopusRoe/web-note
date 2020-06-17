## Slice (切片)

---

在Go语言中，因为 Array数组的长度是固定的，而且数组长度属于类型的一部分，所以在实际运用当中，数组有很多的局限性

Slice 是一个轻量级的数据结构，提供了访问数组子序列(或者全部)元素的功能，而且 Slice 的底层是引用一个 Array数组 对象

Slice是一个引用类型，他的内部结构包含 `地址指针`、`长度len()`、`容量cup()`

1. 地址指针：指向 Slice 中第一个元素对应的底层数组元素的地址
2. 长度len()：对应 Slice 中元素的个数，长度不能超过容量cup()
3. 容量cup()：对应 Slice 中第一个元素在底层数组的位置到底层数组结尾的位置

**注意：** Slice的第一个元素不一定是底层数组的第一个元素，而且多个 Slice 可以共享底层数组

### 切片的定义

```go
var 切片名称 []T
```

```go
var a []string              // 声明一个string切片
var b = []int{}             // 声明一个int切片并初始化
var c = []bool{false,true}  // 声明一个bool切片并初始化

fmt.Println(a)              // []
fmt.Println(b)              // []
fmt.Println(c)              // [false,true]

fmt.Println(a == nil)       // true
fmt.Println(b == nil)       // false
fmt.Println(c == nil)       // false
```

**注意：** Slice 和 Array不同，Slice之间不能比较，Slice 唯一合法的比较操作是和 `nil` 比较

### 切片的长度和容量

Slice 拥有自己的长度和容量，可以使用内置 `len()` 函数求 Slice 的长度，使用内置 `cap()` 函数求 Slice 的容量

### 切片的表达式

切片表达式是从 String、Array、指向 Array 或 Slice 的指针构造子 String 或 Slice。

1. 指定 开始 和 结束 两个索引界限值来构造 Slice
2. 