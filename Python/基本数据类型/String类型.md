## String类型

---

由0个或多个字符组成的有序字符序列

String由一对单引号`'`、一对双引号`"`或三个单双引号包裹起来的有序字符序列

```python
a = 'Python'
b = "Python"
c = '''Python'''
d = """Python"""
```

String是字符的有序序列，可以对其中的字符进行索引与切片

```python
a = 'Python'
b = a[0:-1]
c = a[0]
```

### String类型的序号

方法名称 | | | | | | |
-|-|-|-|-|-|-|
反向递减 | -6 | -5 | -4 | -3 | -2 | -1 |
||P|y|t|h|o|n|
正向递增 | 0 | 1 | 2 | 3 | 4 | 5 |

### 切片的高级用法

<String\>[M:N]，M缺失表示至开头开始，N缺失表示至结尾结束,其中的N是不会包含的

```python
a = 'Python1234123'
b = a[6:]
print(b)        # '1234123'
c = a[:-1]
print(c)        # 'Python123412'
```

<Sting\>[M:N:K]，其中的K表示步长

```python
a = '0123456789'
b = a[1:8:2]
print(b)        # '1357'

c = a[::-1]
print(c)        # '9876543210'
```

### String的操作符

1. **StringX + StringY** 连接字符串X和字符串Y
2. **StringX * n** 复制**n**次字符串X
3. **X in S** 如果 X 是 S的子串，则返回**true**，否则返回**false**

### String的处理函数

#### len('String')

返回String的长度

```python
a = 'Python'
print(len(a))       # 6
```

#### str(x)

把任意形式的 x，转换为String类型

```python
a = 123456789
print(str(a))       # '123456789'
```

#### hex(x)

返回整数x 对应的十六进制数的小写形式String

```python
a = 425
print(hex(a))       # '0x1a9'
```

#### oct(x)

返回整数x 对应的八进制数的小写形式String

```python
a = 425
print(oct(a))       # '0o651'
```

#### chr(x)

返回x数值，所对应的 Unicode 单字符

#### ord(x)

返回 Unicode 单字符所对应的数值

#### eval(x)

把字符串最外围的单引号`'`或双引号`"`删除

```python
a = '"python"'
print(eval(a))        # "python"
```

### String类型内置的方法

方法 | 描述 |
-|-|
String.lower() | 返回 String 的副本，全部字符小写 |
String.upper() | 返回 String 的副本，全部字符大写 |
String.islower() | 当 String 所有字符都是小写，返回 true ，否则返回 false |
String.isprintable() | 当 String 所有字符都是可印的，返回 true ，否则返回 false |
String.isnumeric() | 当 String 所有字符都是 Number 时，返回 true ，否则返回 false |
String.isspace() | 当 String 所有字符都是空格，返回 true ，否则返回 false |
String.endswith(suffix[,start[,end]]) | String[start:end] 以 suffix 结尾返回 true ，否则返回 false |
String.starstwith(prefix[,start[,end]]) | String[start:end] 以 prefix 开始返回 true ，否则返回 false |
String.split(sep=None,maxsplit=-1) | 返回一个 Array，由 String 根据 sep 分割的部分组成，sep默认为空格，maxsplit 为最大分割数 |
String.count(sub[,start[,end]]) | 返回 String[start:end] 中 sub 子串出现的次数 |
String.replace(old,new[,count]) | 返回 String 的副本，所有 old 都被 new 所替换，如果给出 count 则前 count 次的 old 被替换 |
String.center(width[,fillchar]) | 返回新的 String ，并且把旧的 String 居中，其中 width 为长度，fillchar 为填充字符 |
String.strip([chars]) | 返回 String 的副本，并且去除左侧和右侧中 chars 中列出的字符 |
String.zfill(width) | 返回 String 的副本，长度为 width，不足部分在左侧添加 0 |
String.format() | 返回 String 的一种排版格式化 |
String.join(iterable) | 返回一个新的 String ，由 iterable 变量的每个元素组成，元素间用 String 分割 |
