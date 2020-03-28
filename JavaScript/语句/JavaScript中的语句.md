## JavaScript中的语句

---

### if 语句

编程语言中最常用的语句
```javascript
const a = 1
if ( a > 0) {
    return a
} else if ( a > 1){
    return a
} else {
    return a
}
```

### do-while 语句

do-while 语句是一种后测试循环语句，就是在对条件表达式求值之前，循环体内的代码至少会执行一次
```javascript
const i = 0
do {
    i += 1
} while (i < 10)
/* 
i 的初始值为 0，每次循环都会递增 1，只要 i < 10 ,循环就会一直持续下去
 */
```

### while 语句

while 语句是前测试循环语句，就是先对条件表达式求值，然后在执行循环体内的代码
```javascript
const i = 0
while (i < 10) {
    i += 1
}
/* 
i 的初始值为 0，先看i 是不是 < 10，是就执行 i + 1，否就暂停循环
 */
```

### for 语句

for 语句也是前测试循环语句，但是可以在执行循环之前 **初始化变量** **定义条件表达式** **定义循环后表达式**

```javascript
for (let i = 0; i < 10; i++){
    console.log(i)
}
/* 
for 循环其实也可以写成 while 循环
 */
```

for 语句中的 **初始化表达式** **条件表达式** **循环后表达式**都是可选，如下
```javascript
for (;;) {
    console.log('1')
}
/* 
这个 for 循环会一直在控制台输出 '1' ，不会结束
 */
```

### for-in 语句

for-in 语句是一种精准的迭代语句，可以用来枚举对象的属性
```javascript
const a = {a:1,b:2,c:3,d:4,e:5,f:6}
for (let b in a) {
    console.log(b)
}
/* 
这个例子，会把 Object a 中的属性名，一个一个的赋值给 变量 b，然后在控制台，打印出来
 */
```

### label 语句

label 语句可以在代码中添加标签，方便以后使用
```javascript
start: for (let i = 0; i < 10; i++){
    console.log(i)
}
```

### break & continue 语句

break 语句，会立即退出循环，强制继续执行循环后面的语句(不会再执行循环内的代码)
continue 语句，也会立即退出循环，但是退出循环后，会继续从循环的顶部继续执行(会继续执行循环内的代码)
```javascript
let a = 0
for (let i = 0; i < 10; i++){
    if (i % 5 == 0) {
        break;
    }
    a++
}

console.log(a)  // 5
/* 
当 i 的值自增到 5 时，能够整除 5，则执行了 break 语句，
跳出循环并且执行循环后的语句，直接在控制台输出，循环执行的次数 5次
 */

let a = 0
for (let i = 0; i < 10; i++){
    if (i % 5 == 0) {
        continue;
    }
    a++
}

console.log(a)  //9
/* 
当 i 的值自增到 5 时，能够整除 5，则执行了 continue 语句，
跳过了当 i = 5 时的 a++，然后返回循环顶部，
从i = 6 开始继续执行循环，当 for 循环条件表达式的求值为 false时才结束循环，
然后执行循环后的语句，在控制台输出，循环执行的次数 9次

 */
```

**break & continue 语句与 label语句 联合使用时**
```javascript
const a = 0
outermost:
for (let i = 0; i < 10; i++){
    for (let j = 0; j < 10; j++){
        if (i == 5 && j == 5) {
            break outermost;
        }
        a++
    }
}

console.log(a)  //55
/* 
当 i = 5 & j = 5 时，会触发 break 语句，而 break 后跟的outermost
会导致 break 语句 不仅会退出变量j的循环，而且还会退出变量i的循环
然后执行循环后的语句，在控制台输出，嵌套循环的执行次数 55
 */

 const a = 0
outermost:
for (let i = 0; i < 10; i++){
    for (let j = 0; j < 10; j++){
        if (i == 5 && j == 5) {
            continue outermost;
        }
        a++
    }
}

console.log(a)  //95
/* 
当 i = 5 & j = 5 时，会触发 continue 语句，
退出变量j的循环，继续执行变量i的循环，
最后在控制台输出，嵌套循环的执行次数 95
 */
```

### switch 语句
switch 语句 和 if 语句 差不多，都是用于条件判断，但是switch 语句 可以简化 if 语句 的代码
```javascript
/* switch 语句的写法 */
switch (i) {
    case 25: console.log('25'); break;
    case 35: console.log('35'); break;
    case 45: console.log('45'); break;
    default: console.log('other number'); break;
}
/* if 语句的写法 */
if (i == 25){
    console.log('25')
} else if (i == 35){
    console.log('35')
} else if (i == 45){
    console.log('45')
} else {
    console.log('other number')
}
```
在case 后面添加一个 break 语句，可以避免同时执行多个case 代码的情况，但是也可以人为执行多个 case 代码
```javascript
switch (i) {
    case 25:
    case 35: console.log('25 & 35'); break;
    case 45: console.log('45'); break;
    default: console.log('other number'); break;
}
```
case 的值 可以是变量或者表达式
```javascript
const a = 5
const b = 6
switch (true) {
    case a < b: console.log(a); break;
    case a > b: console.log(b); break;
    default: console.log(a,b); break;
}
```

---

嗯，还有个 with 语句，但是在严格模式下，会报错的，而且with 语句大量使用会导致性能下降，就不写了