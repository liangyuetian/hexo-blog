---
title: css-js获取伪元素(Pseudo-Element)属性
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-03-24 21:55:17
tags:
categories: Css
---

用JavaScript获取伪元素(Pseudo-Element)属性的方法

<!-- more -->

[http://www.webhek.com/post/pseudo-element.html](http://www.webhek.com/post/pseudo-element.html)

CSS伪元素(pseudo-elements)非常的有用——你可以用它制作出CSS三角形，用在提示框上面，还可以用它完成很多简单的任务，而不需要多余的HTML元素。以前，伪元素的CSS属性是无法用JavaScript获取的，但现在，有了一个新的JavaScript方法可以访问它们！ 假设你的CSS代码是这样的：
```css
.element:before {
    content: 'NEW';
	color: rgb(255, 0, 0);
}
```
为了获取.element:before里的样式属性，你可以使用下面的JavaScript代码：
```js
var color = window.getComputedStyle(
	document.querySelector('.element'), ':before'
).getPropertyValue('color')
```
将伪元素作为window.getComputedStyle方法的第二个参数，你能获取到伪元素样式里的属性！将这个技巧放入你的知识库里，随着浏览器的发展，伪元素将会变得越来越有用！

> 伪元素一个冒号两个冒号都是对的；两个冒号是css3的标准，用一个冒号是为了兼容低版本