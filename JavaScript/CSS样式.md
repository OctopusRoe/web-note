## CSS 样式

---

使用 JavaScript 设置的 CSS 样式都是内联样式，内联样式的优先级较高，通过 JavaScript 修改后会立即显示，但是如果 CSS样式表中写了 !important，则样式表会有最高优先级，内联样式也无法覆盖

`elementNode.style.styleName`

```javascript
const a = document.getElementById('p')

a.style.backgroundColor = red
```

因为 CSS样式中的 样式名中间有 **-** 而 JavaScript 会识别为 符号 **-** ，所以在 JavaScript 中样式名才用 lower camel case (小驼峰式命名法)

```css
/* CSS 中的 样式名 */
p{
    background-color: black;
}
```

```javascript
/* javascript 中的样式名 */
const a = document.getElementById('p')

a.style.backgroundColor = red
```

### 读取样式

#### getComputedStyle()

此方法用于 IE9 及其他浏览器，读取当前 元素 正在显示的样式

`window.getComputedStyle(elementNode,pseudoElement)`

参数名 | 必须 | 作用 |
-|-|-|
elementNode | 是 | 要获取样式的元素 |
pseudoElement | 否 | 伪类元素，当不查询伪类元素时可以忽略或传入 null |

```javascript
const a = document.getElementById('p')

console.log(window.getComputedStyle(a))
```

#### currentStyle

属性，只支持 IE9 以下,用于读取当前元素正在显示的样式

`elementNode.currentStyle`

这个属性只有 IE 支持，嗯少用，无兼容性问题，就不要用了

### 其他一些 node CSS 属性

属性名 | 作用 |
-|-|
clientWidth | 获取 node 元素的 可见宽度 (包括内边距) |
clientHeight | 获取 node 元素的 可见高度 (包括内边距) |
offsetWidth | 获取 node 元素的 可见宽度 (包括内边距，边框) |
offsetHeight | 获取 node 元素的 可见高度 (包括内边距，边框) |
offsetParent | 获取当前 node 元素 最近开启了 定位的 父元素 |
offsetLeft | 获取 node 元素 水平偏移位置 |
offsetTop | 获取 node 元素 垂直偏移位置 |
scrollHeight | 获取 node 元素 的整体高度 (有滚动条的，包括滚动条内的高度) |
scrollWidth | 获取 node 元素 的整体宽度 (有滚动条的，包括滚动条内的宽度) |
scrollLeft | 获取滚动条水平滚动距离 (元素滚动条) |
scrollTop | 获取滚动条垂直滚动距离 (元素滚动条) |

> scrollHeight - scrollTop = clientHeight 时，说明滚动条到底了 (垂直)
> scrollWidth - scrollLeft = clientWidth 时，说明滚动条到底了 (水平)