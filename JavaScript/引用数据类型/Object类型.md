## Object类型

---

Object类型，就是一组数据和方法的集合,Object是所有它实例的基础

```javascript
/* 两种创建方法 */

/* 通过 Object() 关键字创建 空 Object类型 */
const a = new Object()

/* 通过字面量来创建，并给 Object类型 里赋予属性 */
const b = {c:1,d:2}

/* 添加属性与方法 */
a.e = 3
a[f] = 4

/*
在以上方法中，使用了两种赋值方法
a.e = 3 方法是基本的添加方法
a[f] = 4 方法中的[]内可以放入String
*/

/* 取值方法与赋值方法相同 */
```

### 常用方法

#### hasOwnProperty(propertyName)

用于检查给定的属性在当前对象的实例中是否存在

参数名 | 类型 |
-|-|
propertyName | String|

```javascript
const a = {b:1,c:2}
const b = new a()
b.hasOwnProperty('b')   // true
---------------------------------
b.prototype.d = 3       // 添加属性 d 到 b的原型上
b.hasOwnProperty('d')   // false
```

#### isPrototypeOf(object)

用于检查传入的对象是否是当前对象的原型

参数名 | 类型 |
-|-|
object | Object |

```javascript
const a = {b:1,c:2}
const b = new a()
b.isPrototypeOf(a)  // true
```

#### getPrototypeOf(object)

用于返回指定object的原型(内部Prototype属性的值)

参数名 | 类型 |
-|-|
object | Object |

```javascript
const a = {b:1,c:2}
const b = new a()
Object.getPrototypeOf(b)    // 返回 a
```

#### assign(target,...soures)

用于将所有可枚举的属性值，从一个或多个源对象复制到目标对象，返回目标对象

参数名 | 类型 |
-|-|
target(目标对象) | Object |
soures(源对象) | Object |

```javascript
const a = {b:1,c:2}
const copy = Object.assign({},a)    // {b:1,c:2}
/* 此方法可以用于合并对象 */
const d = {e:3}
const f = {g:4}
const h = Object.assign(d,f)        // {e:3,g:4}
```

#### entries(object)

返回一个给定对象自身可枚举属性的键值对数组，其排列与使用 for...in 循环遍历该对象时返回的顺序一致（区别在于 for-in 循环还会枚举原型链中的属性）

参数名 | 类型 |
-|-|
object | Object |

```javascript
const a = {b:1,c:2,d:3}
const e = Object.entries(a) // {['b',1],['c',2],['d',3]}
```

#### is(value1,value2)

用于判断两个值是否是相同的值

- 下列任何一项成立，则两个值相同

> 1. 两个值都是**undefined**
> 2. 两个值都是**null**
> 3. 连个值都是**true**或者**false**
> 4. 两个值是由相同个数的字符按照相同的顺序组成的String
> 5. 两个值都指向同一个Object
> 6. 都是0，或者都是-0，或者都是NaN,或者都是除了0和NaN外的其他同一个数字

这种相等逻辑判断和，**==** 运算不同，**==** 运算符会对它两边做隐式类型转换(类型不同的话)，然后才进行相等性对比，但是is()不会做这种类型转换，而且与 **===** 也不一样

```javascript
Object.is('a','a')  // true
Object.is([],[])    // true
```

#### keys(object)

方法会返回一个由一个给定对象的自身可枚举属性key组成的数组

参数名 | 类型 |
-|-|
object | Object|

```javascript
const a = [a,b,c,d,e]
Object.keys(a)    // [0,1,2,3,4]

cosnt c = {0:a,1:b,2:c,3:d}
Object.keys(c)  // [0,1,2,3]
```

#### values(object)

方法会返回一个由一个给定对象的自身可枚举属性value组成的数组

参数名 | 类型 |
-|-|
object | Object |

```javascript
const a = {0:a,1:b,2:c,3:d}
Object.values(a)    // [a,b,c,d]
```
