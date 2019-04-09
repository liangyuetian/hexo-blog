---
title: svg-animate元素说明
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-04-09 23:37:16
tags: svg animate
categories: SVG
---

[官方文档](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element/animate)

<!-- more -->


SVG 元素中的动画元素，用来描述某一个属性的动画过程

**官方描述**

> 动画元素放在形状元素的内部，用来定义一个元素的某个属性如何踩着时点改变。在指定持续时间里，属性从开始值变成结束值。

### 全局属性



### 私有属性

* <a href="#attributeName">attributeName</a>
* <a href="#attributeType">attributeType</a>
* <a href="#from">from to</a>
* <a href="#dur">dur</a>
* <a href="#repeatCount">repeatCount</a>


#### <a name="attributeName">AttributeName</a> 动画的属性名

该属性标识了在一个动画动作环节中，父元素的需要被改变的属性名。

例子：
```html
<rect x="50" y="50" width="100" height="100">
    <animate attributeType="XML"
        attributeName="y"
        from="0" to="50"
        dur="5s"/>
</rect>
<!-- 比如这里的y，就是对矩形的长对象一个变化，从0增加到50 -->
```

#### <a name="attributeType">attributeType</a> 
该属性指定目标属性和它相对应的值处于哪个命名空间里。
可取值有
<code>CSS</code> <code>XML</code> <code>auto</code>

**CSS**
指定attributeName的值是一个CSS属性名。
```html
<svg width="120" height="120" viewPort="0 0 120 120" version="1.1">
    <rect x="10" y="10" width="100" height="100" class="red">
        <animate attributeType="CSS" attributeName="fill" from="red" to="green" dur="10s" repeatCount="indefinite" />
    </rect>
</svg>
<!-- 将填充色从 red 变到 green -->
```
 
**XML**
指定attributeName的值是一个XML属性名，在目标元素的默认XML命名空间里。
其实就是元素属性，
```html
<svg width="120" height="120" viewPort="0 0 120 120" version="1.1">
    <rect x="10" y="10" width="100" height="100" class="red">
        <animate attributeType="XML" attributeName="x" from="0" to="100" dur="10s" repeatCount="indefinite" />
    </rect>
</svg>
<!-- 将x坐标从 0 变到 100 -->
```

**auto**
编译器将把attributeName匹配到目标元素的一个属性。用户代理先搜索CSS属性列表以找出一个匹配的属性名，如果找不到，再为这个元素搜索默认XML命名空间。

#### <a name="from">from to</a> 

这个属性是在svg动画发生过程中被修改的属性的初始值。
当同时使用了这个属性和  to 属性, animation将会修改这个这个设定的动画属性的值从from属性的值到to属性的值。

#### <a name="dur">dur</a> 
该属性标识了动画的简单持续时间。
可选值有
<code>clock-value</code> | <code>indefinite</code>

**clock-value**
指定简单持续时间的时长。值必须大于0。可以用小时（h）、分钟（m）、秒（s）、毫秒（ms）表达这个值。
可以组合这些时间表达式以提供一个复合的持续时间，比如这样：hh:mm:ss.iii或者这样：mm:ss.iii。

**indefinite**
不发生改变

#### <a name="repeatCount">repeatCount</a> 
这个属性表示动画将发生的次数。
这个属性的值指定了重复的次数。它也可以包括用分数值表示。它的值必须大于0。
<code>number</code> | <code>indefinite</code>
