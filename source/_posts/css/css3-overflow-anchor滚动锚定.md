---
title: css3-overflow-anchor滚动锚定
abbrlink: 3965827120
date: 2019-09-19 13:03:22
tags: css3 
categories: CSS
---

[https://developer.mozilla.org/en-US/docs/Web/CSS/overflow-anchor](https://developer.mozilla.org/en-US/docs/Web/CSS/overflow-anchor)

```css
section {
  overflow-anchor: auto;
}
```
<!-- more -->


### 为何要有这个属性

滚动锚定是一种浏览器特性，它试图阻止一种常见的情况，在DOM完全加载之前，你可以向下滚动一个网页，当它完成时，任何加载到当前位置的元素都会把你推到页面的下方。

 

这是有道理的，为什么会这样。
有一些CSS属性，我们应用于那些赋予它们大小（例如宽度）、形状（如转换）和位置（例如空白）的元素。
如果这些元素在我们在页面上滚动的时候没有加载，那么DOM将继续加载它们，即使它们位于我们当前的viewport之外，并且会在物理上扩展，为那些新加载的元素腾出空间。
随着DOM的增长，我们在页面上的位置会发生变化，以适应这些元素。

滚动锚定可以防止“跳跃”体验，因为在当前位置上方的DOM中发生变化时，锁定用户在页面上的位置。
这允许用户在页面上停留的位置，即使新元素被加载到DOM中。

> 简单的说就是：我们读文章时，页面内容变化不影响我们阅读，此时的页码位置静止

overflow-anchor属性允许我们在事件中选择退出滚动锚定特性，因为它更倾向于允许内容在加载元素时重新流动。

```css
section {
  overflow-anchor: [auto | none];
}

```
* auto（默认）：滚动事件使页面高度或宽度发生了改变，滚动条不会因此而改变指向的内容，而。允许滚动锚定。
* none：滚动事件使页面高度或宽度发生了改变，滚动条指向的内容也会随之而变。禁止滚动锚定。
