---
title: svg-linearGradient-线性渐变
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-04-22 23:10:07
tags: svg
categories: SVG
---

[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/linearGradient)

<!-- more -->

linearGradient元素用来定义线性渐变，用于图形元素的填充或描边。

```html
<svg width="120" height="120"  viewBox="0 0 120 120">

    <defs>
        <linearGradient id="MyGradient">
            <stop offset="5%"  stop-color="green"/>
            <stop offset="95%" stop-color="gold"/>
        </linearGradient>
    </defs>

    <rect fill="url(#MyGradient)"
          x="10" y="10" width="100" height="100"/>
</svg>
```

<div id="sbhssdkal109032"></div>
<script>
  var a = `
<svg width="120" height="120"  viewBox="0 0 120 120">
    <defs>
        <linearGradient id="MyGradient">
            <stop offset="5%"  stop-color="green"/>
            <stop offset="95%" stop-color="gold"/>
        </linearGradient>
    </defs>
    <rect fill="url(#MyGradient)"
          x="10" y="10" width="100" height="100"/>
</svg>
`;
  sbhssdkal109032.innerHTML = a
</script>

### stop
一个渐变上的颜色坡度，是用stop元素定义的。
stop元素可以是<linearGradient>元素或者<radialGradient>元素的子元素。