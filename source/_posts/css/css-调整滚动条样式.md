---
title: css-调整滚动条样式
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-03-03 18:32:09
tags: css 
categories:
---

一直感觉angular中文文档的滚动条样式非常棒
[https://www.angular.cn/](https://www.angular.cn/)

<!-- more -->

```css
body::-webkit-scrollbar {
    /* 定义了滚动条整体的样式 */
    height: 6px;
    width: 6px
}

body::-webkit-scrollbar-track {
    /* 轨道部分 */
    -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, .3)
}

body::-webkit-scrollbar-thumb {
    /* 滑块部分 */
    background-color: red;
    outline: #333 solid 1px
}
```
