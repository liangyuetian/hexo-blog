---
title: css-scroll-滚动捕捉
tags: css 滚动捕捉 scroll
categories: Css
abbrlink: 3578512530
date: 2019-08-30 16:08:11
---

通过在容器元素上设置scroll-snap-type属性并在其子元素上设置scroll-snap-align属性来使用滚动捕捉。当滚动容器元素时，它将捕捉到你定义的子元素

```css
.container { scroll-snap-type: y mandatory; } 
.child { scroll-snap-align: start; }
```

<!-- more -->

[https://www.w3cplus.com/css/practical-css-scroll-snapping.html](https://www.w3cplus.com/css/practical-css-scroll-snapping.html)

### 设置捕捉关键点
```css
.container { scroll-snap-points-y: repeat(300px); }
```

这种方法非常有限。由于它只允许均匀间隔的捕捉点，因此你无法真正构建捕捉到不同大小元素的接口。
如果元素在不同的屏幕尺寸之间改变形状，你也必然会遇到问题。

或

```css
.container { 
    scroll-snap-type: y mandatory;
    /* 在y轴上设置捕捉点，适合竖直方向上的滚动 */
    /* 在x轴上设置捕捉点 适合水平方向上的滚动*/
    scroll-snap-points-y: repeat(300px);
    scroll-snap-type: y mandatory;
} 

.child { 
    scroll-snap-align: start;
}
```

## 父容器属性

### scroll-snap-type: mandatory vs. proximity

<code>mandatory</code> 属性值意味着每当用户停止滚动时，浏览器必须捕捉到捕捉点。
<code>proximity</code> 属性没有那么严格，它意味着浏览器可能会在看起来合适的情况下突然捕捉到某个位置。根据我的经验，当你在一个捕捉点的几百个像素内停止滚动时，这种情况就会出现。

<code>mandatory</code> 更危险 可以提供更一致的用户体验
<code>proximity</code> 更温和

### scroll-padding

默认情况下，内容会吸附到容器的边缘。你可以通过在容器上设置scroll-padding属性来改变它。它遵循与常规padding属性相同的语法。

如果你的布局中有可能妨碍内容的元素（比如固定的标题），那么这个属性就非常的有用。

```css
.container {
    scroll-padding: 20px;
}
```

## 子元素上的属性

### scroll-snap-align 

这个属性可以让你指定元素哪一部分应该与容器对齐。它有三个值可选择：start、center和end。

这些是相对于滚动方向的。如果是垂直滚动，start指的是元素的顶部边缘。如果是水平滚动条，它指的是左边缘。center和end遵循相同的原则。
你可以为滚动条的不同方向设置不同的值，这两个值之间用空格分隔开

### scroll-snap-stop：normal vs. always

默认情况下，滚动捕捉仅在用户停止滚动时启动，这意味着用户可以在停止之前跳过多个捕捉点

你可以在任何子元素上设置scroll-snap-stop: always来改变它。这会强制滚动容器在该元素上停止，然后用户可以继续滚动。
> 这个特性可以实现滚屏

## 滚动捕捉可以同时在两个方向工作

```css
.container { 
    scroll-snap-type: both mandatory; // both
}

.tile { scroll-snap-align: start; }
```
