---
title: svg-marker绘制的箭头
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
abbrlink: 7d48ccfa
date: 2019-04-11 23:19:43
tags: svg
categories: SVG
---

[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/marker)

<!-- more -->


marker元素定义了在特定的<path>元素、<line>元素、<polyline>元素或者<polygon>元素上绘制的箭头或者多边标记图形。

```html
<svg width="120" height="120" viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg" version="1.1">

    <defs>
        <marker id="Triangle" viewBox="0 0 10 10" refX="1" refY="5" markerWidth="6" markerHeight="6" orient="auto">
            <path d="M 0 0 L 10 5 L 0 10 z" />
        </marker>
    </defs>

    <polyline points="10,90 50,80 90,20" fill="none" stroke="black" stroke-width="2" marker-end="url(#Triangle)" />
</svg>
```
<div id="sbhssdkal109032"></div>
<script>
  var a = `<svg >
    <defs>
        <marker id="Triangle" viewBox="0 0 10 10" refX="1" refY="5" markerWidth="6" markerHeight="6" orient="auto">
            <path d="M 0 0 L 10 5 L 0 10 z" />
        </marker>
    </defs>
    <polyline points="10,90 50,80 90,20" fill="none" stroke="black" stroke-width="2" marker-end="url(#Triangle)" />
</svg>`;
  sbhssdkal109032.innerHTML = a
</script>