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

join() 可以使用不同的分隔符来构建 Array 的输出的字符串
```javascript
const a = [1,2,3,4]
console.log(a.join(''))     // '1234'
console.log(a.join('|'))    // '1|2|3|4'
```