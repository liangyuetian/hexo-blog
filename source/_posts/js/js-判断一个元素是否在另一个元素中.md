---
title: 判断一个元素是否在另一个元素中
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
abbrlink: 183942322
date: 2019-08-29 11:21:10
tags:
categories:
---


<!-- more -->

```html
<div id="parent">
	<div id="children"></div>
</div>
```
```js
let parent = doucument.getElementById('parent');
let children = document.getElementById('children');
```

### 方案一：使用 contains

```js
parent.contains(children);   // true
children.contains(parent);   // false
```

### 方案二

```js
function getTargetNode(ele, target) {
    // ele是内部元素，target是你想找到的包裹元素
    if (!ele || ele === document.body) {
        return false;
    }
    if (ele === target) {
        return true;
    } else {
        return getTargetNode(ele.parentNode, target);
    }
}
getTargetNode(children, parent); // ie9下依旧坚挺
```

兼容方法
```js
let contains = document.documentElement.contains ?
      function(parent, node) {
        return parent !== node && parent.contains(node)
      } :
      function(parent, node) {
        while (node && (node = node.parentNode))
          if (node === parent) return true
        return false
      }
```
