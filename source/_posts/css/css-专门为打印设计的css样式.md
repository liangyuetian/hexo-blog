---
title: css-专门为打印设计的css样式
abbrlink: 806548742
date: 2019-09-10 10:55:18
tags:
categories:
---
分隔符插入位置
page-break-before
page-break-after
page-break-inside

<!-- more -->


```css
.page {
    page-break-before: auto;
    page-break-after: always;
    page-break-inside: auto;
    /** 
        auto 如果需要，在对象之前插入页分割符
        always 始终在对象之前插入页分割符
        avoid 避免在对象前面插入页分割符
        left 在对象前面插入页分割符直到它到达一个空白的左页边
        right 在对象前面插入页分割符直到它到达一个空白的右页边
    */
}
```