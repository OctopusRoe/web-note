## Number类型

---

```javascript
/* 默认是十进制 */
const a = 1
/* 十六进制：以0x开头，后跟0-9数字或者a-f字母(大小写皆可) */
const b = 0x9
/* 浮点数 最高精度是17位*/
const c = 0.1
/* 浮点数可以用于科学计数法 */
const d = 3.156e7   //3.156e7 = 3.156 * 1,000,000
```

### NaN(非数值)

在JavaScript中任何涉及到 NaN的操作，都会返回NaN，而且NaN与任何值都不相等，包括NaN本身
isNaN() 函数，用于判断参数是否是NaN

```javascript
isNaN(NaN)  // true
isNaN(10)   // false
isNaN('1')  // true
```

### 数值转换

- true = 1
- false = 0
- null = 0
- undefined = NaN
- '' = 0

### 常用方法

```javascript
const a = 1 + 2
const b = 1 * 2
const c = 2 / 1
const d = 2 - 1
const e = 26 % 5    //模运算(数学中的余数)
```

- parseInt() 如果字符串前几位是数字，可以使用此方法把字符串的前几位转换为Number类型

```javascript
const a = '1234blue'
parseInt(a) // 结果是Number类型的1234
/* 使用此方法转换空String类型，会返回NaN */
const b = ''
parseInt(b) // 结果为NaN
```

- Namber() 方法用于把对象转换成Number类型

```javascript
const a = '1'
Number(a)   // 结果为Number类型的1
/* 使用Number() 方法转换空String类型，会返回0 */
```

- toString() 把Number类型转换成String类型

```javascript
numberObject.toString()
```
