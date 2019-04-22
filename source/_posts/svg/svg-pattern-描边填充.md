---
title: svg-pattern-描边填充
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-04-22 22:39:48
tags:
categories:
---

[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/pattern)

<!-- more -->

使用预定义的图形对一个对象进行填充或描边，就要用到pattern元素。pattern元素让预定义图形能够以固定间隔在x轴和y轴上重复（或平铺）从而覆盖要涂色的区域。先使用pattern元素定义图案，然后在给定的图形元素上用属性fill或属性stroke引用用来填充或描边的图案。


```html
<svg width="120" height="120" viewBox="0 0 120 120"">
    <defs>
        <pattern id="Triangle" width="10" height="10" patternUnits="userSpaceOnUse">
            <polygon points="10,0 10,10 0,10"/>
	    </pattern>
    </defs>

    <circle cx="60" cy="60" r="50" fill="url(#Triangle)"/>
</svg>
```

<div id="sbhssdkal109032"></div>
<script>
  var a = `
  <svg width="120" height="120" viewBox="0 0 120 120"">
    <defs>
        <pattern id="Triangle" width="10" height="10" patternUnits="userSpaceOnUse">
            <polygon points="10,0 10,10 0,10"/>
	    </pattern>
    </defs>
    <circle cx="60" cy="60" r="50" fill="url(#Triangle)"/>
</svg>
`;
  sbhssdkal109032.innerHTML = a
</script>