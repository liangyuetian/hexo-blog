---
title: 'svg-动画路径,轨迹移动,变幻'
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
tags: svg
categories: SVG
abbrlink: 21984
date: 2019-04-10 23:14:45
---

[官方文档 animateMotion](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/animateMotion)
[官方文档 animateTransform](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/animateTransform)
<!-- more -->

### animateMotion
animateMotion元素导致引用的元素沿着运动路径移动。
```html
<svg width="120" height="120"  viewBox="0 0 120 120"
    xmlns="http://www.w3.org/2000/svg" version="1.1"
    xmlns:xlink="http://www.w3.org/1999/xlink" >
    <path d="M10,110 A120,120 -45 0,1 110 10 A120,120 -45 0,1 10,110"
        stroke="lightgrey" stroke-width="2" 
        fill="none" id="theMotionPath"/>
    <circle cx="10" cy="110" r="3" fill="lightgrey"  />
    <circle cx="110" cy="10" r="3" fill="lightgrey"  />
    <circle cx="" cy="" r="5" fill="red">
        <animateMotion dur="6s" repeatCount="indefinite">
            <mpath xlink:href="#theMotionPath"/>
        </animateMotion>
    </circle>
</svg>
```
<div id="dakjlsdas90312j1"></div>
<script>
    var svgstr = `<svg width="120" height="120"  viewBox="0 0 120 120"
    xmlns="http://www.w3.org/2000/svg" version="1.1"
    xmlns:xlink="http://www.w3.org/1999/xlink" >
    <path d="M10,110 A120,120 -45 0,1 110 10 A120,120 -45 0,1 10,110"
        stroke="lightgrey" stroke-width="2" 
        fill="none" id="theMotionPath"/>
    <circle cx="10" cy="110" r="3" fill="lightgrey"  />
    <circle cx="110" cy="10" r="3" fill="lightgrey"  />
    <circle cx="" cy="" r="5" fill="red">
        <animateMotion dur="6s" repeatCount="indefinite">
            <mpath xlink:href="#theMotionPath"/>
        </animateMotion>
    </circle>
</svg>`
dakjlsdas90312j1.innerHTML = svgstr;
</script>


### animateTransform
animateTransform元素变动了目标元素上的一个变形属性，从而允许动画控制转换、缩放、旋转或斜切。

```html
<svg width="120" height="120" viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg" version="1.1"
    xmlns:xlink="http://www.w3.org/1999/xlink">
    <polygon points="60,30 90,90 30,90">
        <animateTransform attributeName="transform" attributeType="XML" type="rotate" from="0 60 70" to="360 60 70"
            dur="10s" repeatCount="indefinite" ></animateTransform>
    </polygon>
</svg>
```

<div id="dakjlsdasj1"></div>
<script>
    var svgstr2 = `<svg width="120" height="120" viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg" version="1.1"
    xmlns:xlink="http://www.w3.org/1999/xlink">
    <polygon points="60,30 90,90 30,90">
        <animateTransform attributeName="transform" attributeType="XML" type="rotate" from="0 60 70" to="360 60 70"
            dur="10s" repeatCount="indefinite" ></animateTransform>
    </polygon>
</svg>`
dakjlsdasj1.innerHTML = svgstr2;
</script>
