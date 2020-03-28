## JavaScript中的操作符

---

### 一元操作符

1. **递增和递减操作符**
JavaScript的递增和递减有两个版本，前置型和后置型。

类型 | 区别 |
|-|-|
前置型 | 执行前置递增和递减操作时，变量的值都是在语句被求值**以前**改变的 |
后置性 | 递增和递减的操作，都是在包含它们的语句被求值**之后**才执行的

```javascript
const a = 29
const b = ++a + 1   // 31
const c = 29
const d = c++ + 1   // 30
c == 30             // true
/* 这里前置我感觉用的很少=。=，但是后置用的多 */
for(const i = 0, i < 10, i++){
    console.log(i)
}
```

2. **一元加和减操作符**
+号常用来，把纯数字String 快速转换成Number
-号和数学中的一样，表示负值，也能把纯数字的String 转换成Numnber
```javascript
const a = '12'
const b = + a   // Number类型的12
const c = - a   // Number类型的-12
```

### 布尔操作符

1. **逻辑非 (！)**
JavaScript高级中的翻译看的人很难理解，如果按人话来说，就是取反，真变假，假变真。
如果是Boolean类型，就直接取反值。
不是Boolean类型，就先按Boolean类型转换规则，先转换成Boolean类型，在取反
```javascript
console.log(!false)     // true
console.log(!'')        // true
console.log(!'black')   // false
console.log(!0)         // true
console.log(!123)       // false
console.log(!NaN)       // true
console.log(!undefined) // true
console.log(!null)      // true
```

2. **逻辑与 (&&)**
以人话来说，在都是Boolean类型的情况下，只有条件都为true，结果才为true，只要有一个条件为false，结果都是false
```javascript
const a = 1
const b = 2
if( a == 1 && b == 2 )  // true
if( a == 1 && b < 2 )   // false
if( a < 0 && b == 2 )   // false
```
在有一个操作数不是Boolean类型的情况下，按下表规则走
第一个操作数 | 第二个操作数 | 返回结果 |
-|-|-|
Object | Boolean | Boolean |
Boolean(求值结果为true) | Object | Object|
Boolean(求值结果为false) | Object | false |
Object(第一个) | Object(第二个) | Object(第二个) |
null | | null |
NaN | | NaN |
undefined | | undefined |

逻辑与 属于短路操作，只要第一个操作数能够决定结果，就不会对第二个操作数求值。只要第一个操作数为false，不管第二个操作数是啥，结果都是false。
```javascript
const a = true
const b = false
if(a && b)              // false
if(b && a)              // false
```

3. **逻辑或 (||)**
按人话来说，就是语文的 “或者”，只要有一个条件为true，结果就为true，只有条件都为false，结果才是false
```javascript
const a = 1
const b = 2
if( a == 1 || b == 2 )  // true
if( a == 1 || b < 2 )   // true
if( a < 0 || b == 2 )   // true
if( a < 0 || b < 2 )    // false
```
在有一个操作数不是Boolean类型的情况下，按下表规则走
第一个操作数 | 第二个操作数 | 返回结果 |
-|-|-|
Object | | Object |
Boolean(false) | Object | Object |
Object(第一个) | Object(第二个) | Object(第一个) |
null | null | null |
NaN | NaN | NaN |
undefined | undefined | undefined |

逻辑或 属于短路操作，只要第一个操作数的求值结果为true，就不会对第二个操作数求值

### 乘性操作符

1. **乘法 (*)**
嗯，就是数学的乘法，如果出现特殊情况，则按下表规则走

特殊情况 | 结果 |
-|-|
超出 ECMAScript 数值 | Infinity \|\| -Infinity |
Infinity * 0 | NaN |
Infinity * !0 | Infinity \|\| -Infinity |
Number * NaN | NaN |
Infinity * Infinity | Infinity |
Infinity * !Number | 后台自动调用 Number() 然后按特殊情况规则走

2. **除法 (/)**
嗯，就是数学的除法，如果出现特殊情况，按下表规则走

特殊情况 | 结果 |
-|-|
超出 ECMAScript 数值 | Infinity \|\| -Infinity |
Number / NaN | NaN |
0 / 0 | NaN |
Infinity / Infinity | NaN |
!0有限数 / 0 | Infinity \|\| -Infinity |
!0 / Infinity | Infinity \|\| -Infinity |
Infinity / !Number | 后台自动调用 Number() 然后按特殊情况规则走

3. **求模 (%)**
按人话来说就是数学的余数，如果出现特殊情况，按下表规则走

特殊情况 | 结果 |
-|-|
Infinity / Number < Infinity | NaN |
Number < Infinity / 0 | NaN |
Infinity / Infinity | NaN |
(Number < Infinity) / Infinity | (Number < Infinity) |
0 / Number | 0 |
!Number & Number | 后台自动调用 Number() 然后按特殊情况规则走 |

### 加性操作符

1. **加法 (+)**
嗯，就是数学中的加法，如果出现特殊情况，按下表规则走

特殊情况 | 结果 |
-|-|
NaN + Number | NaN |
Infinity + Infinity | Infinity |
-Infinity + -Infinit | -Infinit |
Infinity + -Infinit | NaN |
String + String | String |
Number + String | String |
!String & Number | 后台自动调用 toString() 然后拼串 |
Undefined \|\| Null | 后台调用 String() 然后拼串 |
```javascript
const a = 5
const b = '5'
const c = a + b     // '55'
```

2. **减法 (-)**
还是数学中的减法，如果出现特殊情况，按下表规则走

特殊情况 | 结果 |
-|-|
NaN - Number | NaN |
-Infinity - -Infinity | NaN |
Infinity - -Infinity | Infinity |
-Infinity - Infinity | -Infinity |
!Number & Number | 后台调用 Number() 然后按特殊情况走 |
Object & Number | 后台调用 valueOf() 如果取的是NaN 则返回NaN |
Object & Number | 没 valueOf() 调用 toString() 把 String 转换成 Number |

### 关系操作符 (< & > & <= & >=)

嗯，还是数学中的规则，不过在编程语言中，关系操作符返回的是Boolean

特殊情况 | 比较方法 |
-|-|
都是 Number | 执行数值比较 |
都是 String | 比较 String 的字符编码值 |
Number & !Number | !Number 转换成 Number 比较 |
Number & Boolean | Boolean 转换成 Number 比较 |
Number & Object | 后台调用 valueOf() 然后进行比较 |
Number & Object | 没 valueOf() 调用 toString() 然后进行比较 |
Number & NaN | 都是 false |

比较 String 的时候，其实比较的是字符编码值，在这样的话，会出现一些奇怪的问题
```javascript
const a = '23' < '3'    // true
const b = '23' < 3      // false
```

### 相等操作符

1. **相等和不相等 (== & !=)**
如果两个操作数相等，而且使用 **==** 来比较，则返回 **true**，否则返回 **false**。如果两个操作数不相等，而且使用 **!=** 来比较，则返回 **true**，否则返回 **false**。
> 这两个操作符都会先转换操作数(**强制转换**)，然后在比较它们的相等性

在转换不同的数据类型时，以下列规则走

类型 | 规则 |
-|-|
Boolean | 先转换成 Number,false == 0,true == 1 |
String & Number | 先把 String 转换成 Number |
Object & Object | 比较是否是同一个对象，如果都指向同一个对象则返回true，否则返回false |

特殊情况的比较结果

表达式 | 结果 |
-|-|
null == undefined | true |
'NaN' == NaN | false |
5 == NaN | false |
NaN == NaN | false |
NaN != NaN | true |
false == 0 | true |
true == 1 | true |
undefined == 0 | false |
null == 0 | false |
'5' == 5 | true |

2. **全等和不全等 (=== & !==)**
全等和不全等 **在比较之前不会转换操作数** ，**===** 只在在操作数 **未经转换就相等** 的情况下才返回 **true**，**!==** 只在操作数 **未经转换就不相等** 的情况下才返回 **true**
```javascript
const a = ('55' == 55)  // true
const b = ('55' === 55) // false
```
> 在强调一次 **=== & !== 在比较前不会对操作数进行类型转换，而 == & != 会在比较前对操作数进行类型转换** 

### 条件操作符

嗯，可以简化 if 语句的输入，我个人很喜欢在优化代码的时候使用，但是不能写的太复杂，不然以后会看的头大
```javascript
const a = booleanExpression ? trueValue : falseValue
```
按Js高级上的解释来说，就是对 booleanExpression 的求值结果，决定给变量 a 赋予 trueValue 还是赋予 falseValue

### 赋值操作符

就是用 = 把右边的值赋予给左侧的变量
```javascript
const a = 10
```
复合赋值
```javascript
const a += b    // a = a + b
const a -= b    // a = a - b
const a *= b    // a = a * b
const a /= b    // a = a / b
const a %= b    // a = a % b
```