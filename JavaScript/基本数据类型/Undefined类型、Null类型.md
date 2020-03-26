## Undefined类型、Null类型

---

在JavaScript中 Undefined类型只有一个特殊的值，**undefined**
* 其表示，在**声明变量**，但**未对**变量加以**初始化赋值**时，这个变量的值就是undefined
```javascript
const a
console.log(a)  // undefined
```

Null类型是JavaScript中第二个只有一个特殊值的数据类型，**null**
* 其表示一个**空对象的指针**
* 如果定义一个变量准备在以后使用时，可以将变量初始化为 null 
* Undefined 派生自 Null
```javascript
undefined == null   // true
undefined === null  // false
```