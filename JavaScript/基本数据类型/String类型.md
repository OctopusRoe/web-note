## String类型

---

```javascript
const a = 'this is javascript'
const b = "this is javascript too"
const c = 1 + '1'
/* 结果为'11'string + number = string */
const d = '1' + '2'
/* 结果为'12'string +string = string */
```

### 常用方法

#### indexOf()

用于检索字符串是否含有指定的内容

```javascript
stringObject.indexof(searchvalue,fromindex)
/*
searchvalue: 必须,用于规定需要检索的字符串值
fromindex: 可选，规定在字符串中开始检索的位置 0 < fromindex < stringObject.length-1
*/
```

#### includes()

用于检索字符串是否含有指定的内容，返回Boolean

```javascript
stringObject.includes(String,Number)
/*
String：必须，用于规定需要检索的字符串值
Number: 可选，规定在字符串中开始检索的位置
*/
```

#### startsWith()

用于检索字符串头部是否含有指定的内容，返回Boolean

```javascript
stringObject.startsWith(String,Number)
/*
String：必须，用于规定需要检索的字符串值
Number: 可选，规定在字符串中开始检索的位置
*/
```

#### endWith()

用于检索字符串尾部是否含有指定的内容，返回Boolean

```javascript
stringObject.endWith(String,Number)
/*
String：必须，用于规定需要检索的字符串值
Number: 可选，规定在字符串中开始检索的位置  
*/
```

#### split()

把字符串分隔成为字符串数组

```javascript
stringObject.split(separator,howmany)
/*
separator: 必须，regex 或者 string 从该参数分割stringObject
howmany: 可选，number 规定返回的arrray[string].length < howmany
*/
```

#### 消除字符串头部和尾部的空格

- trim() 用于消除字符串头部和尾部的空格
- trimStart() 用于消除字符串头部的空格
- trimEnd() 用于消除字符串尾部的空格

```javascript
const a = ' a '
a.trim()        // 'a'
a.trimStart()   // 'a '
a.trimEnd()     // ' a'
```

#### slice()

用于提取stringObject的某个部分，并以新的stringObject返回被提取的部分

```javascript
stringObject.slice(start,end)
/*
start: 要抽取的片段下标，0 < start 从左开始，0 > start 从右开始
end: 同start，0 > end 从右开始
*/
```

#### 改变字符串大小写

- toLowerCase() 把stringObject变小写
- toUpperCase() 把stringObject变大写

```javascript
stringObject.toLowerCase()
stringObject.toUpperCase()
/* toLowerCase() 配合 indexOf() 可以快速的判断浏览器版本 */
const a = navigator.userAgent
const b = a.toLowerCase().indexOf('chrome') != -1 // 结果是个boolean
```

#### valueOf()

返回stringObject的原始值

```javascript
const a = '31'
const b = a.valueOf() // 结果是个 number 类型的31
```

#### toString()

返回stringObject

```javascript
const a = 42
const b = a.toSrting() // 结果是'42'
```

#### String()

返回stringObject，可以把 null 和 undefined 转换为字面量

```javascript
const a = null
const b = a.String() // 结果是sring 'null'
```

#### match()

找到一个或多个regex的匹配，返回一个Array

```javascript
stringObject.match(regex)   // 返回一个Array
```

#### replace()

替换与regex匹配的子串

```javascript
stringObject.replace(regex)
```

#### search()

检索与regex相匹配的值

```javascript
stringObject.search(regex)
```
