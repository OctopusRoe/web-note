## DOM对象

---

嗯，DOM就是 Document Object Model(文档对象模型)，通过这个，我们可以用JavaScript很方便的操作Html标签。

### Html DOM Document Object (获取对象)

#### getElementById()

通过 Html 标签的 id 名，来获取一个 element node object

```html
<!-- html 标签 -->
<p id='element-name'>123</p>
```

```javascript
/* javascript 代码 */
document.getElementById('element-name')
```

#### getElementsByTagName()

通过 **标签名** 获取一组 element node list object

```html
<!-- html 标签 -->
<p>123</p>
<p>123</p>
<p>123</p>
```

```javascript
/* javascript 代码 */
document.getElementsByTagName('p')
```

#### getElementsByName()

通过 **指定名** 获取一组 element node list object

```html
<!-- html 标签 -->
<p name='element-name'>123</p>
<p name='element-name'>123</p>
<p name='element-name'>123</p>
```

```javascript
/* javascript 代码 */
document.getElementsByName('element-name')
```

#### querySelector()

根据 css 选择器的字符串来获取一个 element node object，如果满足条件的元素有多个，只会返回第一个

```html
<!-- html 标签 -->
<p class='class-name'>123</p>
```

```javascript
/* javascript 代码 */
document.querySelector('class-name')
```

#### querySelectorAll()

根据 css 选择器的字符串来获取一组 element node list object

```html
<!-- html 标签 -->
<p class='class-name'>123</p>
<p class='class-name'>123</p>
<p class='class-name'>123</p>
```

```javascript
/* javascript 代码 */
document.querySelectorAll('class-name')
```

### Html DOM Node Object (获取元素节点的子节点)

#### getElementsByTagName()

通过 **标签名** 获取一组当前节点的指定标签名子节点

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = doucument.getElementById('div')
a.getElementsByTagName('p')
```

#### childNodes

节点属性，表示当前节点的所有子节点，类数组对象

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = doucment.getElemengById('div')
console.log(a.childNodes)
```

#### firstChild

节点属性，表示当前节点的第一个子节点

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p>123</p>
    <p>123</p>
</div>
```

```javascript
const a = doucment.getElementById('div')
console.log(a.firstChild)
```

#### lastChild

节点属性，表示当前节点的最后一个子节点

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = doucument.getElementById('div')
console.log(a.lastChild)
```

#### chirldren

节点属性，表示当前节点的所有元素节点，类数组对象

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = document.getElementById('div')
console.log(a.children)
```

> childNodes 与 children 的区别
> 1. childNodes 会包含 所有的子节点，包括文本节点、注释节点
> 2. children 只会包含 元素节点
> **所以别用 childNodes 属性**

### Node Object 获取父节点和兄弟节点

#### parentNode

节点属性，表示当前节点的父节点

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p id='p'>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = document.getElementById('p')
console.log(a.parentNode)
```

#### previousSibling

节点属性，表示当前节点的 **前** 一个兄弟节点

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p id='p'>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = document.getElementById('p')
console.log(a.previousSibling)
```

#### nextSibling

节点属性，表示当前节点的 **后** 一个兄弟节点

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p id='p'>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = document.getElementById('p')
console.log(a.nextSibling)
```

### Html DOM Document Object (修改对象)

#### appendChild()

把新的子节点，添加到指定的节点

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = document.getElementById('div')

a.appendChild('p')
```

#### removeChild()

删除指定的子节点

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p id='p'>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = document.getElementById('div')

a.removeChild(a.children[0])
a.removeChild(document.getElementById('p'))
```

#### replaceChild()

替换指定的子节点 

`fatherNode.replaceChild(newNode,oldNode)`

```html
<!-- html 标签 -->
<div id='div'>
    <p>123</p>
    <p id='p'>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = document.getElementById('div')

const b = document.createTextNode('234')     // 创建节点

a.replaceChild(b,document.getElementById('p'))
```

#### insertBefore()

在指定的子节点前，插入新的子节点

`fatherNode.insertBefore(newNode,childNode)`

```html
<!-- html 标签 -->
<div id='div'>
    <p id='p'>123</p>
    <p>123</p>
    <p>123</p>
</div>
```

```javascript
/* javascript 代码 */
const a = document.getElementById('div')

/* 创建新的文本节点，创建新的 p节点 */
const b = document.createTextNode('234')
const c = document.createElement('p')

c.appendChild(b)                // 把文本节点 添加到 p节点 下

a.insertBefore(c,document.getElementById('p'))
```