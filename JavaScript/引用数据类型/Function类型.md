## Function类型

---

每个 **函数** 都是 Function类型的实例，而且 **函数名** 实际上也是一个指向 **函数对象**的指针

```javascript
/* 函数的创建方法 */

/* 第一种 函数 表达式(也叫匿名 函数) */
const a = function () {}

/* 第二种 函数 声明 */
function sum () {}

/* 匿名函数不赋值给变量，而是自执行(IIFE) */
(function(){}())

/* 声明构造函数 (首字母大写) */
function Person(){}
const a = new Person()
```

### 函数声明 与 函数表达式的区别

- 函数声明 会函数声明提升，这就导致可以在函数声明之前调用函数
- 函数表达式 不会进行函数提升，在定义函数前调用 函数 会导致程序出错

```javascript
/* 函数声明 */
sum()                       // 会正常运行
function sum () {}

----------------------------

/* 函数表达式 */
a()                         // 程序报错
const a = function () {}
```

### 函数的内部属性

#### arguments

arguments 是一个 **类** Array Object，包含传入 function 中的所有 **参数**

- 取值方法与 Array 一样，靠下标索引取值
- 内部有一个 callee 属性，指向拥有该 arguments 对象的 函数(严格模式报错)

```javascript
const a = function () {
    return arguments[0] + arguments[1]
}

a(1,2)                      // 3

----------------------------

const b = function () {
    console.log(arguments.callee === b)
}

b()                         // true
```

#### this

this 引用的是 函数执行 的环境对象(在网页全局作用域下，this 对象引用的就是 windows)

```javascript
const a = function () {console.log(this)}

a()                         // windows
```

#### length

length 属性表示函数希望接收的参数个数

```javascript
const a = function (value) {}
const b = function (value1,value2) {}
const c = function () {}

console.log(a.length)       // 1
console.log(b.length)       // 2
console.log(c.length)       // 0
```

#### prototype

嗯，就是原型，在ECMAScript2015 以前，坑爹的继承是靠这玩意写的，写起来反人类，读起来也反人类，一点都不优美

- 主要用于存放一些公共的方法和属性，方便继承
- 含有 constuctor 属性，指向 prototype 所在函数的指针
**class 继承不香嘛，为什么要自己和自己过不去**

```javascript
function Person () {}
Person.prototype.sayHi = function () {console.log('hi')}

const a = new Person()
const b = new Person()
console.log(a.say === b.say)        // 返回 true 说明是同一个对象

console.log(Person.prototype.constuctor === Person)
/* 同样是返回 true，说明这两玩意是同一个对象 */
```

### 函数的方法

#### apply()

使用 apply() 方法调用函数，可以手动设置函数体内的 this 对象，接收两个参数

- 参数一：运行函数的作用域
- 参数二：参数 Array，可以是 Array 实例，也可以是 arguments 对象

```javascript
function test (one,two) {}

function applyTest (value1,value2) {
    test.apply(windows,arguments)
    test.apply(windows,[value1,value2])
}
```

#### call()

使用 call() 方法调用函数，可以手动设置函数体内的 this 对象，接收参数与apply() 方法不同

- 参数一：运行函数的作用域
- 其他参数都需要直接传递给 函数

```javascript
function test (one,two) {}

function callTest (value1,value2) {
    test.call(this,value1,value2)
}
```

#### bind()

bind() 方法调用函数，会创建该函数的一个实例，传入的 this 值 会绑定到实例上,直接调用实例时，不在需要用 call() & apply() 动态的传入 this 值

```javascript
window.color = 'blue'

const a = { color: 'red'}

function sayColor (){
    console.log('this = ' + this)
    console.log(this.color)
}

const testBind = sayColor.bind(a)

sayColor()                  // this = windows, blue
testBind()                  // this = a, red
```
