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

> [https://lon.im/post/css-print.html](https://lon.im/post/css-print.html)

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

## 使用媒体查询来专门设置打印时的样式

### @media print

```css
body {
        background-color: white;
    }
    img {
        visibility: hidden;
    }
    a::after {
        content: "(" attr(href) ")"; /* 所有链接后显示链接地址 */
    }
```

### 在css中导入打印时专有样式

```css
@import url("my-print-style.css") print;
```

### 使用link引入打印时专有样式
```HTML
<link rel="stylesheet" media="print" href="my-print-style.css">
```

## @page

<code>@page</code>可以设置页面的大小
支持的属性有：margin、size、marks、bleed 以及页面外边距盒子等

### 设置页面外边距盒子

页面外边距盒子需要在 @page 下使用，使用起来和伪类类似，也包含 content 属性。
```css
@page {
    /* 页面内容区域底部添加一条 1px 的灰线 */
    @bottom-left, @bottom-center, @bottom-right {
        border-top: 1px solid gray;
    }

    /* 页脚中间显示格式如 "第 3 页" 的页码 */
    @bottom-center {
        content: "第" counter(page) "页";
    }
}
```

#### margin 系列属性（margin-top、margin-right、margin-bottom、margin-left 和 margin）用于指定页面外边距大小。
```css
@page {
    size: A4 portrait;
    margin: 3.7cm 2.6cm 3.5cm; /* 国家标准公文页边距 GB/T 9704-2012 */
}
```

#### size 属性支持 auto、landscape、portrait、<length>{1,2} 和 <page-size>。

* 默认值为 auto，表示页面大小和方向由用户代理决定
* landscape 指定页面为横向，如果 <page-size> 没有指定，大小则由用户代理决定
* portrait 指定页面为纵向，如果 <page-size> 没有指定，大小则由用户代理决定
* <length>{1,2} 表示指定页面大小，填写两个值则分别指定页面盒子的宽度和高度，填写一个值则同时指定宽度和高度。在 CSS3 中，值的单位支持em 和 ex，大小相对于页面上下文中字体的大小
* <page-size> 也用于指定页面大小，等价于使用 <length>{1,2}。常用的值有：A3、A4、A5、B4 和 B5 等，详细尺寸请参考 ISO 216。* * <page-size> 可以与 landscape 或 portrait 组合同时指定页面方向。

### 伪类
页面上下文也支持使用伪类，其中支持的伪类有：:left、:right、:first 和 :blank。
#### 伪类 :left 和 :right
需要双面打印时，通常需要将左页和右页设置不同的样式（如页边距、页码位置）。这时左页和右页可以分别用 :left 和 :right 表示。再次强调，通过 :left 和 :right 设置左右页面不同样式，并不代表用户代理会将页面双面打印

```css
/* 通过分别设置左页和右页不同的左右页面距，为装订边留出更多的空间 */

@page :left {
    margin-left: 2.5cm;
    margin-right: 2.7cm;
}

@page :right {
    margin-left: 2.7cm;
    margin-right: 2.5cm;
}
```

### 伪类 :first
#### 伪类 :first 用于匹配到文档的第一页。
```css
@page :first {
    margin-top: 10cm; /* 首页上页边距设置为 10cm */
}
```
#### 伪类 :blank
伪类 :blank 用于匹配文档的空白页。

```css
h1 {
    page-break-before: left; /* 一级标题强制分配到右页 */
}

@page :blank {
    @top-center {
        content: "这是空白页";
    }
}
```

注意，空白页既可能是左页，又可能是右页，设置左页或右页的样式也会显示在空白页上，如果不希望显示在空白页上，可以清除这些样式。

```css
h1 {
    break-before: left;
}

@page :left {
    @left-center {
        content: "这是左页";
    }
}

@page :right {
    @right-center {
        content: "这是右页";
    }
}

@page :blank {
    @left-center, @right-center {
        content: none; /* 如果是空白页则不显示 */
    }
}
```

## 分页

page-break-before，page-break-after，page-break-inside (CSS 2.1)
用于控制元素之前、之后或之中是否分页，没有生成盒子的块元素不会生效。

page-break-before、page-break-after 属性支持 auto、always、avoid、left、right、recto 和 verso。

```css
h2 {
    page-break-before: always;
}
```

* auto 默认值，表示既不强制分页也不禁止分页
* always、avoid 表示在该元素之前（或之后）强制或禁止分页
* left、right 表示在该元素之前（或之后）强制分页，使得下一页出现在左页或右页
* recto、verso 页面进度从左至右时，分别与 right 和 left 一致；反之与 left 和 right 一致

page-break-inside 属性仅支持 auto 和 avoid，表示在元素内允许或禁止分页。
```css

thead, tfoot {
    display: table-row-group;
}
thead, tfoot, tr, th, td {
    page-break-inside: avoid;
}
```

### orphans，windows (CSS 2.1)
orphans 和 windows 用于指定在页面的底部或顶部，元素中允许剩余的最少行数，默认为 2 行。


##  最佳实践

* “白纸黑字”--避免不必要的背景颜色、加深文字颜色等
* 避免打印次要的内容，比如导航栏、侧边栏等
* 链接后显示链接地址
* 做好分页，避免标题、表格单元格等换行

```css
@media print {
    @page {
        size: A4 portrait;
        margin: 3.7cm 2.6cm 3.5cm;
    }

    h1 {
        page-break-before: always;
    }

    h1, h2, h3, h4, h5, h6,
    thead, tfoot, tr, th, td,
    li {
        page-break-inside: avoid;
    }

    body {
        background-color: white;
        color: black;
    }

    nav, aside {
        display: none;
    }

    a::after {
        content: "(" attr(href) ")";
    }

    thead, tfoot {
        display: table-row-group;
    }
}
```
