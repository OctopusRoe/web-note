## Array类型

---

嗯，Array类型，就是数据的有序列表，其中的每一项都可以保存任何类型的数据(String,Number,Boolean,Object,Array,Null,undefined)

```javascript
/* 两种创建方法 */

/* 通过 Array() 关键字创建 括号中可以填入数字，表示Array类型的长度 */
const a = new Array()

/* 通过字面量来创建 空的 Array类型 */
const b = []

/* 通过下标索引 给 Array类型 添加数据 */
a[0] = 1
b[0] = 2
b[1] = 3

/* 取值方法和赋值方法一样 */
```

每个 Array类型 都有一个长度属性 length ,它表示 Array 中一共有多少项数

```javascript
const a = [1,2,3,4,5,6,7,8,9]
console.log(a.length)   // 控制台输出 9
```

但是！length 属性不是 readonly ，可以通过手动修改 length 来给 Array 添加新项或移除项

```javascript
/* 通过修改 lenght 长度来移除最后一项 */
const a = [1,2,3,4,5]
console.log(a.length)   // 5
console.log(a[4])       // 5
a.length = 4
console.log(a[4])       // undefined

/* 通过修改 lenght 长度来添加项 */
const b = [1,2,3]
console.log(b.length)   // 3
b.lenght = 4
console.log(b[3])       // undefined
```

在 Array类型中 下标索引的最大值 永远是 Array.length - 1，通过这个，我们可以很方便的给 Array 末尾添加新项

```javascript
const a = [1,2,3]
a[a.length] = 4
a[a.length] = 5
console.log(a)          // [1,2,3,4,5]
```

检查是否是 Array类型

```javascript
const a = [1,2,3]
console.log(Array.isArray(a))    // true
```

### 常用方法

#### join()

可以使用不同的分隔符来构建 Array 的输出的字符串

```javascript
const a = [1,2,3,4]
console.log(a.join(''))     // '1234'
console.log(a.join('|'))    // '1|2|3|4'
```

#### push()

接收任意数量的参数，把参数逐个添加到 Array 末尾，返回修改后的 Array 的 length

```javascript
const a = [1,2,3,4,5]
const b = a.push(6,7,8)
console.log(a.length)       // 新长度 8
console.log(b)              // 返回数组a的新长度 8
```

#### pop()

从 Array 末尾移除最后一项，减少 Array 的 length 值，返回移除的项

```javascript
const a = [1,2,3,4,5,6]
const b = a.pop()
console.log(a.length)       // 新长度 5
console.log(b)              // 返回移除的项 6
```

> 使用 push() & pop() 方法可以提供 Array 栈方法(后进先出)

#### shift()

移除 Array 的第一项，并返回该项，同时减少 length 的值

```javascript
const a = [1,2,3,4,5,6]
const b = a.shift()
console.log(a.length)       // 新长度 5
console.log(b)              // 返回移除的项 1
```

> 使用 push() & shift() 方法可以提供 Array 队列方法(先进先出)，末尾进前端出

#### unshift()

接收任意数量的参数，把参数逐个添加到 Array 前端，返回修改后的 Array 的 length

```javascript
const a = []
const b = a.unshift(1,2,3,4,5)
console.log(a.length)       // 新长度 5
console.log(b)              // 返回数组a的新长度 5
console.log(a)              // [1,2,3,4,5]
const b = a.unshift(0)
console.log(a)              // [0,1,2,3,4,5]
```

> 使用 unshift() & pop() 方法可以提供 Array 的反向队列方法，前端进末尾出

#### reverse()

反转 Array 项的顺序

```javascript
const a = [1,2,3,4,5]
a.reverse()
console.log(a)              // [5,4,3,2,1]
```

#### sort()

方法用于 Array 排序，默认情况下，按升序排列，否则向 sort() 方法传入一个比较函数，**比较函数接收两个参数，参数一位于参数二前返回 负数，参数一位于参数二后返回 正数，相等返回 0**

```javascript
const a = [9,2,4,5,3,8,7,1,6]

/* sort() 方法默认升序排列 */
a.sort()
console.log(a)              // [1,2,3,4,5,6,7,8,9]

/* 使用 sort() 方法改变成降序排列 */
function test(valueone,valuetwo){
    return valueone < valuetwo ? 1 : -1
}
a.sort(test)
console.log(a)              // [9,8,7,6,5,4,3,2,1]
```

#### concat()

基于当前的 Array 的所有项，创建一个新的 Array ，可以接受1个或多个参数

```javascript
const a = [1,2,3,4,5]
const b = a.concat(6,7,8)
console.log(b)              // [1,2,3,4,5,6,7,8,9]
const c = a.concat(6,[7,8])
console.log(c)              // [1,2,3,4,5,6,7,8,9]
```

#### slice()

基于当前的 Array 中的一个或多个项创建一个新的 Array ，接受1个或2个参数(起始位置和结束位置)

```javascript
const a = [1,2,3,4,5,6,7,8,9]

/* 接受一个参数，则视为起始位置，返回起始位置到末尾的所有项 */
const b = a.slice(5)
console.log(b)              // [6,7,8,9]

/* 接受两个参数，返回起始位置到结束位置 -1 直接的项 */
const c = a.slice(2,7)
console.log(c)              // [3,4,5,6,7]
```

#### splice()

方法会改变原 Array，并返回一个新的 Array ，该 Array 中包含从原始 Array 中删除的项，未删除任何项，则返回空 Array
**Array.splice(start,howmany[,value1,value2,...])**

参数名 | 作用 |
-|-|
start | 起始位置 |
howmany | 个数 |
value1,value2,...| 要添加的项|

> 用于删除：Array.splice(start,howmany)

```javascript
const a = [1,2,3,4,5]
const b = a.splice(0,3)
console.log(a)              // [4,5]
console.log(b)              // [1,2,3]
/* splice(0,3) 表示从索引为0的项开始，删除3项 */
```

> 用于插入：Array.splice(start,howmany[,value1,value2,...])

```javascript
const a = [1,2,3]
const b = a.splice(0,0,'a','b','c','d')
console.log(a)              // ['a','b','c','d',1,2,3]
console.log(b)              // [] 未删除项，所以返回空 Array
const c = [1,2,3]
const d = c.splice(0,0,[4,5,6])
console.log(c)              // [[4,5,6],1,2,3]
```

> 用于替换：Array.splice(start,howmany[,value1,value2,...])

```javascript
const a = [1,2,3,4]
const b = a.splice(1,2,'a','b')
console.log(a)              // [1,'a','b',4]
console.log(b)              // [2,3]
```

#### indexOf()

从前往后检索是否含有指定的项，检索成功返回位置索引，未成功返回 -1
**Array.indexof(searchvalue,fromindex)**

参数名 | 必须 | 作用 |
-|-|-|
searchvalue | 是 | 用于指定需要检索的项 |
fromindex | 否 | 用于指定开始检索的位置索引 |

```javascript
const a = [1,2,3,4,5,6]
console.log(a.indexOf(4))   // Number 4 的索引 3
```

#### lastIndexOf()

从后往前检索是否含有指定的项，检索成功返回位置索引，未成功返回 -1
**Array.lastIndexOf(searchvalue,fromindex)**

参数名 | 必须 | 作用 |
-|-|-|
searchvalue | 是 | 用于指定需要检索的项 |
fromindex | 否 | 用于指定开始检索的位置索引 |

```javascript
const a = [1,2,3,4,5,6]
console.log(a.lastIndexOf(2))   // Number 2 的索引 1
```

#### every()

对 Array 中的每一项运行给定函数，如果该函数对每一项的求值都返回 **true** ，则返回 **true**，否则返回 **false**
**Array.every( function(item,index,array){} )**

参数名 | 必须 | 作用 |
-|-|-|
item | 是 | Array 的项 |
index | 否 | Array 项的索引 |
array | 否 | Array |

```javascript
const a = [1,2,3,4,5]
const b = a.every(item => {return item > 0})
const c = a.every(item => {return item > 2})
console.log(b)              // true
console.log(c)              // false
```

#### some()

对 Array 中的每一项运行给定函数，如果该函数对任意一项的求值返回 **true**，则返回 **true**
**Array.some( function(item,index,array){} )**

参数名 | 必须 | 作用 |
-|-|-|
item | 是 | Array 的项 |
index | 否 | Array 项的索引 |
array | 否 | ArrayObject |

```javascript
const a = [1,2,3,4,5]
const b = a.every(item => {return item > 2})
const c = a.some(item => {return item > 2})
console.log(b)              // false
console.log(c)              // true
```

#### filter()

对 Array 中的每一项运行给定的函数，返回该函数求值为true的项所组成的 Array
**Array.filter( function(item,index,array){} )**

参数名 | 必须 | 作用 |
-|-|-|
item | 是 | Array 的项 |
index | 否 | Array 项的索引 |
array | 否 | ArrayObject |

```javascript
const a = [1,2,3,4,5,6,7,8,9,4,3,2,1]
const b = a.filter(item => {return item > 2})
console.log(b)              // [3,4,5,6,7,8,9,4,3]
```

#### map()

对 Array 中的每一项运行给定的函数，返回每次函数调用的结果所组成的 Array
**Array.map( function(item,index,array){} )**

参数名 | 必须 | 作用 |
-|-|-|
item | 是 | Array 的项 |
index | 否 | Array 项的索引 |
array | 否 | ArrayObject |

```javascript
const a = [1,2,3,4,5,6]
const b = a.map(item => {return item * 2})
console.log(b)              // [2,4,6,8,10,12]
```

#### forEach()

对 Array 中的每一项运行给定的函数，没有返回值(for 循环的简写版)
**Array.forEach( function(item,index,array){} )**

参数名 | 必须 | 作用 |
-|-|-|
item | 是 | Array 的项 |
index | 否 | Array 项的索引 |
array | 否 | ArrayObject |

```javascript
const a = [1,2,3,4]
a.forEach(item => {console.log(item)})
```

#### reduce()

从前往后迭代 Array 的所有项，然后构建一个最终的返回值
**Array.reduce( function(first,second,index,array){},defaultNumber )**

参数名 | 必须 | 作用 |
-|-|-|
first | 是 | 前一个值 |
second | 是 | 后一个值|
index | 否 | 项的索引 |
array | 否 | ArrayObject |
defaultNumber | 否 | 基础值 |

```javascript
const a = [1,2,3,4,5,6,7]
const b = a.reduce((first,second) => {return first + second})
console.log(b)              // 28
const c = a.reduce((first,second) => {return first + second},30)
console.log(c)              // 58
```

#### reduceRight()

从后往前迭代 Array 的所有项，然后构建一个最终的返回值
**Array.reduceRight( function(first,second,index,array){},defaultNumber )**

参数名 | 必须 | 作用 |
-|-|-|
first | 是 | 前一个值 |
second | 是 | 后一个值|
index | 否 | 项的索引 |
array | 否 | ArrayObject |
defaultNumber | 否 | 基础值 |

```javascript
const a = [1,2,3,4,5,6,7]
const b = a.reduceRight((first,second) => {return first + second})
console.log(b)              // 28
const c = a.reduceRight((first,second) => {return first + second},30)
console.log(c)              // 58
```
