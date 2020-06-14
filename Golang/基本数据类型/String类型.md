## String类型

---

Go语言中的 string 以原生数据类型出现，使用 string 就像使用其他原生数据类型(int、boolean、float32等)一样。Go语言里的 string 内部实现使用 `UTF-8` 编码。String的值为 `双引号(")`中的内容。

**注意：** 和 JavaScript 不同之处在于 `单引号(')` 中的类型为 byte类型 而不是 string类型。

```go
a := "hello world"      // String类型
b := 'H'                // byte类型
```

### String转义符

Go语言中的常用转义符

转义符 | 含义 |
-|-|
\r | 回车符(返回行首) |
\n | 换行符 |
\t | 制表符 |
\\' | 单引号 |
\\" | 双引号 |
\\\ | 反斜杠 |

### 多行 String

Go语言中的多行 String，还是用 反引号

```go
a := `第一行
第二行
第三行
`
```

**注意：** 反引号间的换行将被作为 String 中的换行，但是所有的转义符均无效，文本会按照多行 String 的样式，原样输出。

### byte类型 rune类型

组成每个 String 的元素叫做 “字符”，可以通过遍历或者单个获取 String 元素获得字符，字符使用 单引号(') 包裹起来。

```go
a := 'x'                // byte 类型

b := '中'               // rune 类型
```

Go语言的字符有以下两种：

1. uint8类型，或者叫 byte类型，代表了 ASCII码 的一个字符
2. rune类型，代表一个 UTF-8字符

Go 使用特殊的 rune类型  来处理 unicode，让基于 Unicode 的文本处理更为方便，也可以使用 byte类型 进行默认 string 处理。

UTF-8编码 中的汉字由 3-4个 byte组成

string 底层是一个 `[]byte`，string 是不能修改的

#### 修改 string

要修改 string 需要先将其转换成 `[]rune` 或者 `[]byte`，完成后在转换为 `string`。无论是哪种转换，都会重新分配内存，并复制底层 `[]byte`。

```go

/* []byte 类型转换 */

a := "big"
b := []byte(a)
b[0] = 'p'
fmt.Println(string(b))          // pig

/* rune 类型转换 */

c := "大傻瓜"
d := []rune(c)
d[0] = '小'
fmt.Println(string(d))          // 小傻瓜

```

### String 的常用方法

#### len() 方法

方法名 | 介绍 |
-|-|
len(str) | 求 String 长度 |

```go
a := "hello"
fmt.Printp(len(a))      // 5
```

#### 拼串的方法

方法 | 介绍 |
-|-|
\+ 或 fmt.Sprintf | 拼接 String |

```go
import "fmt"

a := "hello"
b := "world"

a = a + b                       // "helloworld"
a += b                          // "helloworld"
a = fmt.Sprintf("%v %v", a, b)  // hello world
```

#### strings.Split() 方法

方法 | 介绍 |
-|-|
strings.Split() | 分割 String |

```go
import "strings"

a := "hello"

b := strings.Split(a, "")

fmt.Println(b)                  // ['h' 'e' 'l' 'l' 'o']
```

#### strings.Contains() 方法

方法 | 介绍 |
-|-|
strings.Contains() | 判断是否包含 |

```go
import "strings"

a := "hello"

fmt.Println(strings.Contains(a, "e"))   // true
```

#### strings.HasPrefix() & strings.HasSuffix() 方法

方法 | 介绍 |
-|-|
strings.HasPrefix(), strings.HasSuffix() | 判断 前缀/后缀 是否包含 |

```go
import "strings"

a := "hello"

fmt.Println(strings.HasPrefix(a, "h"))  // true

fmt.Println(strings.HasSuffix(a, "o"))  // true
```

#### strings.Index() & strings.LastIndex() 方法

方法 | 介绍 |
-|-|
strings.Index(), strings.LastIndex() | 子串出现的位置 |

```go
import "strings"

a := "hello"

fmt.Println(strings.Index(a, "l"))      // 2

fmt.Println(strings.Index(a, "x"))      // 不存在返回 -1

fmt.Println(strings.LastIndex(a, "l"))  // 3

```

#### strings.Join() 方法

方法 | 介绍 |
-|-|
strings.Join(a[]string, sep string) | join操作 |

```go
import "strings"

a := []string{'h','e','l','l','o'}

fmt.Println(strings.Join(a, ""))        // hello
```


