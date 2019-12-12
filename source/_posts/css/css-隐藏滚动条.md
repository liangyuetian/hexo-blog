---
title: css-隐藏滚动条
abbrlink: 2631211152
date: 2019-08-30 17:01:29
tags: 
categories: CSS
---

对于Chrome，Safari 和 Opera 而言，可以使用以下的 CSS：

```css
.container::-webkit-scrollbar {
    display: none;
}
```
IE 或 Edge 可用以下代码:

```css
.container {
    -ms-overflow-style: none;
}
```
至于 Firefox，很不幸，没有任何办法隐藏滚动条。
