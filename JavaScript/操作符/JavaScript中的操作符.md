## JavaScript中的操作符

---

##### 一元操作符

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

##### 布尔操作符
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
if(a && b)              // true
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

4. **乘法 (*)**
嗯，就是数学的乘法，如果出现特殊情况，则按下表规则走

特殊情况 | 结果 |
-|-|
超出 ECMAScript 数值 | Infinity \|\| -Infinity |
Infinity * 0 | NaN |
Infinity * !0 | Infinity \|\| -Infinity |
Number * NaN | NaN |
Infinity * Infinity | Infinity |
Infinity * !Number | 后台自动调用 Number() 然后按特殊情况规则走
5. **除法 (/)**
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
6. **求模 (%)**
按人话来说就是数学的余数，如果出现特殊情况，按下表规则走

特殊情况 | 结果 |
-|-|
