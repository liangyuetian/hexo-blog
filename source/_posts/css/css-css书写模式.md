---
title: css/css书写模式
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
abbrlink: 3871472345
date: 2019-06-03 16:54:47
tags:
categories:
---

[https://www.w3cplus.com/css/css-writing-modes.html](https://www.w3cplus.com/css/css-writing-modes.html)
[https://www.w3cplus.com/css3/vertical-typesetting-revisited.html](https://www.w3cplus.com/css3/vertical-typesetting-revisited.html)

<!-- more -->

[你所不知道的CSS Overflow Module_CSS, overflow, text-overflow](https://www.w3cplus.com/css/css-overflow-module.html)
[CSS书写模式](https://www.w3cplus.com/css/css-writing-modes.html)
[CSS的逻辑属性对盒模型带来的变化](https://www.w3cplus.com/css3/vertical-typesetting-revisited.html)

```css
h1 { 
    writing-mode: horizontal-tb; /* 水平方向自上而下的书写方式。即 left-right-top-bottom */
    writing-mode: vertical-rl; /* 垂直方向自右而左的书写方式。即 top-bottom-right-left */
    writing-mode: vertical-lr; /* 垂直方向内内容从上到下，水平方向从左到右 */
    writing-mode: sideways-rl; /* 内容垂直方向从上到下排列 */
    writing-mode: sideways-lr; /* 内容垂直方向从下到上排列 */
}
```

文本溢出方向依赖 direction: rtl | ltr
```html
<div dir=rtl>1234567890</div>
```

使用text-overflow属性实现单行文本溢出添加省略号需满足下面相关条件：

1 该元素必须为 display:block 或者 display:inline-block 元素（或者功能对等，比如Flex项目）
2 元素必须得设置了width、max-width或flex-basis
3 该元素必须设置 overflow: hidden | scroll | auto
4 文本溢出方向依赖 direction: rtl | ltr 属性
5 整行溢出依赖 white-space: nowrap 属性
