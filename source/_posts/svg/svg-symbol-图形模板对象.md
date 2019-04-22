---
title: svg-symbol-图形模板对象
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-04-22 23:00:01
tags: svg
categories: SVG
---

[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/symbol)

<!-- more -->

symbol元素用来定义一个图形模板对象，它可以用一个<use>元素实例化。symbol元素对图形的作用是在同一文档中多次使用，添加结构和语义。结构丰富的文档可以更生动地呈现出来，类似讲演稿或盲文，从而提升了可访问性。注意，一个symbol元素本身是不呈现的。只有symbol元素的实例（亦即，一个引用了symbol的 <use>元素）才能呈现。

```html
<svg>
<symbol id="sym01" viewBox="0 0 150 110">
  <circle cx="50" cy="50" r="40" stroke-width="8" stroke="red" fill="red"/>
  <circle cx="90" cy="60" r="40" stroke-width="8" stroke="green" fill="white"/>
</symbol>

<use xlink:href="#sym01"
     x="0" y="0" width="100" height="50"/>
<use xlink:href="#sym01"
     x="0" y="50" width="75" height="38"/>
<use xlink:href="#sym01"
     x="0" y="100" width="50" height="25"/>
</svg>
```

<div id="sbhssdkal109032"></div>
<script>
  var a = `
<svg>
<symbol id="sym01" viewBox="0 0 150 110">
  <circle cx="50" cy="50" r="40" stroke-width="8" stroke="red" fill="red"/>
  <circle cx="90" cy="60" r="40" stroke-width="8" stroke="green" fill="white"/>
</symbol>

<use xlink:href="#sym01"
     x="0" y="0" width="100" height="50"/>
<use xlink:href="#sym01"
     x="0" y="50" width="75" height="38"/>
<use xlink:href="#sym01"
     x="0" y="100" width="50" height="25"/>
</svg>
`;
  sbhssdkal109032.innerHTML = a
</script>