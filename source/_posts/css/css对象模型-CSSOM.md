---
title: css对象模型-CSSOM
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
abbrlink: d328933a
date: 2019-08-28 16:40:07
tags:
categories:
---

CSSOM是一组允许JavaScript操作CSS的API。它非常类似于DOM，但是用于CSS而不是HTML。它允许用户动态读取和修改CSS样式。

<!-- more -->

[https://www.w3cplus.com/javascript/cssom-css-typed-om.html](https://www.w3cplus.com/javascript/cssom-css-typed-om.html)
[https://www.w3.org/TR/cssom-1/](https://www.w3.org/TR/cssom-1/)


## 设置css方法

### 1 ele.style.protypeName

我们可以使用相同的格式添加或更改页面上任何对象的CSS：ele.style.propertyName，其中ele指的是DOM元素，propertyName指的是希望给ele元素要添加的样式属性
>记住，带有-中划线的CSS属性需要改用陀峰形式，比如上面示例中的background-color属性要写成backgroundColor

> 注意，在动态设置float属性时，需要使用cssFloat，这是因为float是JavaScript中的一个关键词。
> 这个有点类似于getAttribute()给HTML元素设置for属性时，需要使用htmlFor。
```js
$0.style.backgroundColor='green'
```

### 2 $0.attributeStyleMap
在CSS Houdini中的CSSOM，我们可以使用.attributeStyleMap属性来替代ele.style。可以使用ele.attributeStyleMap.set(property, value)来设置元素内联样式：

```js
$0.attributeStyleMap.set('background-color', 'green')
```
其得到的效果和ele.style.property = value等同的效果。另外，.attributeStyleMap类似于Map对象，所以它们支持对象常有的一些方法，比如get、set、keys、values和entry等。这样让我们的工作也变得更为灵活：

```js
$0.attributeStyleMap.set('background-color', 'green') // 设置background-color的值为green
$0.attributeStyleMap.get('background-color').value === 'green' // => false
$0.attributeStyleMap.has('background-color') // => true
$0.attributeStyleMap.delete('background-color') // => 删除background-color
$0.attributeStyleMap.clear() // => 删除所有样式

```

### 3 $0.style.cssText

```js
$0.style.cssText = 'color: red; background-color: blue'

```

## 获取计算样式

### 1 window.getComputedStyle()

我们可以使用window.getComputedStyle()方法获取元素上任何CSS的计算值。

```js
window.getComputedStyle($0).backgroundColor // => "rgb(0, 128, 0)"

window.getComputedStyle(el).backgroundColor;
window.getComputedStyle(el)['background-color'];
window.getComputedStyle(el).getPropertyValue('background-color');
```

### 2 使用el.computedStyleMap()

```js
el.computedStyleMap().get('opacity').value // => 0.5
```

> 注意，window.getComputedStyle()和ele.computedStyleMap()的差别是，前者返回的是解析值，而后值返回计算值。类如，如果你的样式中有一个这样的值，width: 50%，那么在Typed OM中将保留百分值（width: 50%）;而CSSOM中返回的是解析值（width: 200px

> window.getComputedStyle() 返回50% 原始值 可以检索伪元素
> ele.computedStyleMap() 返回200px 计算值

### 3 检索伪元素
```js
window.getComputedStyle(ele, '::before').property;

```

### 4 setProperty() 该方法可以给CSS的属性设置一个新的值

其中property指的是CSS属性，value设置的属性的值，priority允许设置CSS的权重，即!important。比如下面这个示例：
```js
$0.style.setProperty('color', 'red', 'important')

window.getComputedStyle($0).color  // => "rgb(255, 0, 0)"
```

### 5 getPropertyPriority() 返回css属性的优先级

getPropertyPriority()方法是一个很有意思的方法。这个方法会根据传入的CSS属性，返回一个DOMString来表示该属性的优先级。如果有的话，则返回important；如果不存在的话，返回空字符串
```js
$0.style.setProperty('border', '2px solid red', 'important')
$0.style.setProperty('background-color', 'orange')
$0.style.getPropertyPriority('border') // => "important" 
$0.style.getPropertyPriority('background-color') // => ""
```

### 6 removeProperty() 移除style对象的一个属性

```js
$0.style.removeProperty('margin') // => "" $0.style.getPropertyValue('margin') // => ""

```

## CSSStyleSheet 接口

```js
document.styleSheets
```
遍历出来所有运用到的样式表的相关信息
```js
for(let i = 0; i < document.styleSheets.length; i++) { 
    console.log(document.styleSheets[i]) 
}
```

CSSStyleSheet主要有两个属性

* cssRules：返回样式表中CSS规则的CSSRuleList对象
* ownerRule：如果一个样式表示通过@import规则引入document的，则ownerRule将返回那个CSSImportRule对象，否则返回null

cssRules 非常有用，可以遍历出所有的css选择器
```js
let myRules = document.styleSheets[0].cssRules; 
for (i of myRules) { 
    if (i.type === 1) { 
        console.log(i.selectorText)
    } 
}
```

### insertRule 向样式表中插入一条新规则

```js
let firstStylesheet = document.styleSheets[0]
console.log(firstStylesheet.cssRules.length) // => 116 
firstStylesheet.insertRule( `
    body { 
        background-color: orange; 
        font-size: 3em; padding: 2em; 
    }`, firstStylesheet.cssRules.length // 插入位置，插入到最后一个位置
)
```
stylesheet.insertRule()方法接受两值参数：

rule：一个字符串，也就是想插入的样式规则，包含选择器和对应的样式规则
index：一个数字，表示要插入的位置，这是一个可选参数


### deleteRule 从当前样式表对象中删除指定的样式规则

```js
firstStylesheet.deleteRule(116)

```
deleteRule()方法相对来说更为容易，它只接受一个参数index。index就是一个数字，用来指定样式规则的位置。作为参数传入的所选index必须小于cssRules.length，否则将抛出错误。