## Boolean类型

---

Boolean类型 只有两个值 **true** 和 **false**
- 在JavaScript中所有类型的值都有与这两个Boolean值相等的值
Boolean() 方法可以把其他值转换为Boolean值
```javascript
const a = 1
Boolean(a)
```
转换规则如下表格

 数据类型 | 转换为true的值 | 转换为false的值 |
-| -| -|
 Boolean | true | false |
 String | 任何非空String类型 | ""(空String类型) |
 Number | 任何非0的数值(包括无穷大) | 0和NaN |
 Object | 任何对象 | null |
 Undefined | 不适用 | undefined |

**非空既真，非0即真**
