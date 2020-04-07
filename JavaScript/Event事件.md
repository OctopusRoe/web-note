## Event事件

---

当 event 事件的响应 function 被触发时，浏览器每次都会将一个 event object 作为参数传递进响应 function，**event object中封装了当前事件相关的一切信息**

#### event object 属性和方法

属性名/方法名 | 类型 | 作用 |
-|-|-|
bubbles | Boolean | 表明事件是否冒泡 |
cancelable | Boolean | 表明是否可以取消 event 事件的默认行为 |
currenTarget | Element | 其 event 事件处理程序当前正在处理事件的那个 element 元素 |
preventDefault() | Function | 取消事件的默认行为，如果 cancelable 属性是 true，则可以用此方法 |
stopPropagation() | Function | 取消事件的进一步捕获或者冒泡，如果 bubbles 属性为 true，则可以用此方法 |
stopImmediatePropagation() | Function | 取消事件的进一步捕获或冒泡，同时阻止任何 event 事件程序被调用 |
target | Element | event 事件的目标 element 元素 |
type | String | 被触发的 event 事件 的类型 |

> IE8 中响应函数被触发时，浏览器不会传递 event object 作为实参而是将 event object 作为 window object 的属性保存

### Event 事件传播

#### 事件冒泡

指 event 事件向上传导，当子元素 (childElement) 上的 event 事件被触发，其父元素 (fatherElement) 的相同事件也会被触发

如果不希望事件冒泡，可以通过 event object 来取消 `eventObject.cancelBubble = true`

```javascript
document.getElementById('btn').onclick = function (event) {
    event = event || window.event       // 用于判断浏览器是否传递了 event object
    event.cancelBubble = true
}
```

在 event object 中有一个 **target** 属性，作用是返回触发的 event 的 node

#### 事件捕获

与事件冒泡相反，event 事件向下传导，从父元素向子元素传递 event 事件

### 事件绑定方法

#### 单一事件绑定

`elementNode.evetName = function () {}`

```javascript
doucment.getElementById('btn').onclick = function () {}
```

此方法只能向指定的 element node 绑定一个回调函数

#### addEventListener()

此方法，只能在 IE9+ 及其他浏览器使用，可以向指定的 element node 绑定多个回调函数和事件，**先绑定的 event 先执行**

`elementNode.addEventListener(eventName,function,boolean)`

参数名 | 必须 | 作用 |
-|-|-|
eventName | 是 | String类型的事件名称，**注意：不要使用"on"前缀** |
function | 是 | 回调函数 |
boolean | 否 | 指定事件是否在捕获或冒泡阶段执行 (**true**，在捕获阶段执行。**false**，在冒泡阶段执行) |

```javascript
const a = document.getElementById('btn')

function eventFunction () {
    return false
}

a.addEventListener('click', eventFunction, false)
a.addEventListener('mouseup', eventFunction, false)
```

#### attachEvent()

此方法，只在 IE8 以下使用，**后绑定的 event 先执行**

`elementNode.attachEvent(eventName,funciont)`

参数名 | 必须 | 作用 |
-|-|-|
eventName | 是 | String类型的事件名，**注意：要使用"on"前缀** |
funciont | 是 | 回调函数 |

```javascript
const a = document.getElementById('btn')

function eventFunction () {
    return false
}

a.attachEvent('click', eventFunction)
a.attachEvent('mouseup', eventFunction)
```

#### addEventListener() & attchEvent() 的 this 值

- addEVentListener() 方法调用后，**this** 值是调用的 element node (谁调用，就是谁)
- attchEvent() 方法调用后，**this** 值是 window

如果要修改 **this** 值，需要把浏览器自动传递调用，改成匿名 function 调用，然后内部再次调用传递函数 call() 方法，来修改 **this** 值

```javascript
const a = document.getElementById('btn')

function eventFunction () {
    return false
}

a.attachEvent('click', function () {
    eventFunction.call(a)
})
```

### 常用 event 事件

事件名称 | 作用 |
-|-|
onclick | 最常用 event 事件，单机某个对象时，调用回调函数 |
onmousedown | 鼠标按钮被按下时，调用回调函数 |
onmouseup | 鼠标按钮被松开时，调用回调函数 |
onmousemove | 鼠标被移动时，调用回调函数 |
onmouseout | 鼠标从某个元素上移开时，调用回调函数 |
onmouseover | 鼠标移动到某歌元素时，调用回调函数 |
onkeydown | 键盘按键被按下时，调用回调函数 |
onkeyup | 键盘按键被松开时，调用回调函数 |
onkeypress | 键盘被按下并被松开时，调用回调函数 |
touchstart | 手机用 event 触摸开始的时候触发 |
touchmove | 手机用 event 在屏幕上滑动的时候触发 |
touchend | 手机用 event 触摸结束的时候触发 |

- 如果要取消默认行为，可以在 event 事件的回调函数最后 return false
- 但是使用 addEventListener() 方法绑定的响应函数，取消默认行为要使用 eventObject.preventDefault()
  
```javascript
document.getElementById('btn').addEventListener('click',function (event) {
    event.preventDefault()
},false)
```