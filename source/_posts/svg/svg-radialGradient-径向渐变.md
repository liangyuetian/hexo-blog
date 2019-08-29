---
title: svg-radialGradient-径向渐变
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
tags: svg
categories: SVG
abbrlink: 651820336
date: 2019-04-22 23:14:00
---

[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/radialGradient)

<!-- more -->

radialGradient 用来定义径向渐变，以对图形元素进行填充或描边。


```html
<svg width="220" height="150" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <linearGradient id="PadGradient"
                    x1="33%" x2="67%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </linearGradient>
    <linearGradient id="ReflectGradient" spreadMethod="reflect"
                    x1="33%" x2="67%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </linearGradient>
    <linearGradient id="RepeatGradient" spreadMethod="repeat"
                    x1="33%" x2="67%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </linearGradient>
  </defs>
 
  <rect fill="url(#PadGradient)"
          x="10" y="0" width="200" height="40"/>
  <rect fill="url(#ReflectGradient)"
          x="10" y="50" width="200" height="40"/>
  <rect fill="url(#RepeatGradient)"
          x="10" y="100" width="200" height="40"/>
</svg>
```

<div id="sbhssdkal109032"></div>
<script>
  var a = `
<svg width="220" height="150">
  <defs>
    <linearGradient id="PadGradient"
                    x1="33%" x2="67%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </linearGradient>
    <linearGradient id="ReflectGradient" spreadMethod="reflect"
                    x1="33%" x2="67%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </linearGradient>
    <linearGradient id="RepeatGradient" spreadMethod="repeat"
                    x1="33%" x2="67%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </linearGradient>
  </defs>
 
  <rect fill="url(#PadGradient)"
          x="10" y="0" width="200" height="40"/>
  <rect fill="url(#ReflectGradient)"
          x="10" y="50" width="200" height="40"/>
  <rect fill="url(#RepeatGradient)"
          x="10" y="100" width="200" height="40"/>
</svg>
`;
  sbhssdkal109032.innerHTML = a
</script>

### spreadMethod控制不同方式渐变

* pad 
* reflect 
* repeat 重复


```html
<svg width="340" height="120" xmlns="http://www.w3.org/2000/svg">
  <defs>
   <radialGradient id="RadialPadGradient"
                    cx="75%" cy="25%" r="33%" 
                    fx="64%" fy="18%" fr="17%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </radialGradient>
    <radialGradient id="RadialReflectGradient"
                    spreadMethod="reflect"
                    cx="75%" cy="25%" r="33%" 
                    fx="64%" fy="18%" fr="17%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </radialGradient>
    <radialGradient id="RadialRepeatGradient"
                    spreadMethod="repeat"
                    cx="75%" cy="25%" r="33%" 
                    fx="64%" fy="18%" fr="17%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </radialGradient>
  </defs>
 
  <rect fill="url(#RadialPadGradient)"
        x="10" y="10" width="100" height="100"/>
  <rect fill="url(#RadialReflectGradient)"
        x="120" y="10" width="100" height="100"/>
  <rect fill="url(#RadialRepeatGradient)"
        x="230" y="10" width="100" height="100"/>
</svg>

```

<div id="sbhssdkal109033"></div>
<script>
  var b = `
<svg width="340" height="120" xmlns="http://www.w3.org/2000/svg">
  <defs>
   <radialGradient id="RadialPadGradient"
                    cx="75%" cy="25%" r="33%" 
                    fx="64%" fy="18%" fr="17%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </radialGradient>
    <radialGradient id="RadialReflectGradient"
                    spreadMethod="reflect"
                    cx="75%" cy="25%" r="33%" 
                    fx="64%" fy="18%" fr="17%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </radialGradient>
    <radialGradient id="RadialRepeatGradient"
                    spreadMethod="repeat"
                    cx="75%" cy="25%" r="33%" 
                    fx="64%" fy="18%" fr="17%">
      <stop offset="0%"  stop-color="fuchsia"/>
      <stop offset="100%" stop-color="orange"/>
    </radialGradient>
  </defs>
 
  <rect fill="url(#RadialPadGradient)"
        x="10" y="10" width="100" height="100"/>
  <rect fill="url(#RadialReflectGradient)"
        x="120" y="10" width="100" height="100"/>
  <rect fill="url(#RadialRepeatGradient)"
        x="230" y="10" width="100" height="100"/>
</svg>
`;
  sbhssdkal109033.innerHTML = b
</script>
