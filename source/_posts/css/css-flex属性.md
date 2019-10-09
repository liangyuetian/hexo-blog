---
title: css-flex属性
tags: css
categories: CSS
abbrlink: 4045953967
date: 2019-10-09 14:26:44
---

{% asset_img Snipaste_2019-10-09_14-27-51.png %}

<!-- more -->

在Flexbox中，flex属性是最令人难以琢磨的，也是最为复杂的一部分。该属性主要由flex-grow、flex-shrink和flex-basis三个属性组成。这三个属性可以控制一个Flex项目，主要表现在以下几个方面：

* flex-grow：Flex项目的扩展比率，让Flex项目得到（伸张）多少Flex容器多余的空间（Positive free space）
* flex-shrink：Flex项目收缩比率，让Flex项目减去Flex容器不足的空间（Negative free space）
* flex-basis：Flex项目未扩展或收缩之前，它的大小是多少


大部分情形之下，我们都是使用flex属性来设置Flex项目的伸缩的值。其常见值的效果有：

* flex: 0 auto和flex:initial，这两个值与flex: 0 1 auto相同，也是初始值。会根据width属性决定Flex项目的尺寸。当Flex容器有剩余空间时，Flex项目无法扩展；当Flex容器有不足空间时，Flex项目收缩到其最小值min-content。
* flex: auto与flex: 1 1 auto相同。Flex项目会根据width来决定大小，但是完全可以扩展Flex容器剩余的空间。如果所有Flex项目均为flex: auto、flex:initial或flex: none，则Flex项目尺寸决定后，Flex容器剩余空间会被平均分给是flex:auto的Flex项目。
* flex: none与flex: 0 0 auto相同。Flex项目根据width决定大小，但是完全不可伸缩，其效果和initial类似，这种情况下，即使在Flex容器空间不够而溢出的情况之下，Flex项目也不会收缩。
* flex: <positive-number>（正数）与flex: 1 0px相同。该值使Flex项目可伸缩，并将flex-basis值设置为0，导致Flex项目会根据设置的比例因子来计算Flex容器的剩余空间。如果所有Flex项目都使用该模式，则它们的尺寸会正比于指定的伸缩比。


[https://www.w3cplus.com/css/does-it-work-using-the-new-css-layout.html](https://www.w3cplus.com/css/does-it-work-using-the-new-css-layout.html)
{% asset_img flex-19.png %}

{% asset_img flex-20.png %}

🔜

