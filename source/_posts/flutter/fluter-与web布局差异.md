---
title: fluter-与web布局差异
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-02-02 17:08:32
tags: flutter
categories:
---

[https://flutterchina.club/web-analogs/](https://flutterchina.club/web-analogs/)
HTML / CSS代码片段映射到的Flutter / Dart代码片段。
flutter 盒子模型都为border-box
HTML文档以HTML DOCTYPE开始, 并且所有HTML的box-sizing为 border-box, 以便与Flutter模型一致

<!-- more -->

官方文档
[https://flutterchina.club/web-analogs/](https://flutterchina.club/web-analogs/)

## 基本常量
* <font style="color: green">FontWeight</font> FontWeight.w900 字体宽度集
* <font style="color: green">Colors</font> Colors.grey[300] 颜色集
* <font style="color: green">Alignment</font> Alignment.center 旋转原点 transform-origin
* <font style="color: green">TextAlign</font> TextAlign.center 文本对齐
* <font style="color: green">BoxShape</font> BoxShape.circle 圆和椭圆和矩形

## 基本class类，用于实现布局
* new Container 容器，div,section
* new BoxDecoration 容器的约束条件,常用于背景 一般挂载到decoration属性上
* new Center 创建一个弹性盒子，display: flex
* new Text 创建一个文字块，span
* new EdgeInsets 设置内边距，挂载到padding属性下
* new Stack 创建一个盒子，内容为绝对定位盒子列表，children
* new Positioned 创建一个绝对定位盒子
* new Matrix4 3D变幻属性

## 进行基本的布局

### 给文本添加样式 背景色
```dart
var container = new Container( // 一个盒子相当于div
  child: new Text(
    "Lorem ipsum",
    style: new TextStyle(
      fontSize: 24.0
      fontWeight: FontWeight.w900, // 文本粗细
      fontFamily: "Georgia", // 字体
      textAlign: TextAlign.center, // 文本对齐
    ),
  ),
  width: 320.0,
  height: 240.0,
  // color: Colors.grey[300], // 背景色 与下方的数据任选其一
  decoration: new BoxDecoration( // 背景色
    color: Colors.red[400], // 背景色
  ),
);
```
### 文字居中，flex布局
Center widget 可以使其子widget在水平和垂直方向居中.
为了在CSS中实现类似效果，父元素使用flex或table-cell。此页面上的示例使用了flex布局。
```js
var container = new Container( // grey box
  child:  new Center(
    child:  new Text(
      "Lorem ipsum",
      style: bold24Roboto,
    ),
  ),
  width: 320.0,
  height: 240.0,
  color: Colors.grey[300],
);
// new Center 相当于
// display: flex;
// align-items: center;
// justify-content: center; 
```

### 设置容器宽度
要指定Container的宽度，请设置其width属性。 这是一个固定的宽度，不像CSS中max-width属性，它可以设置容器宽度最大值。要在Flutter中模拟这种效果，请使用容器的constraints属性。 创建一个新的BoxConstraints来设置minWidth或maxWidth。

对于嵌套容器，如果父级的宽度小于子级宽度，则子级容器将自行调整大小以匹配父级
```js
var container = new Container( // grey box
  child: new Center(
    child: new Container( // red box
      child: new Text(
        "Lorem ipsum",
        style: bold24Roboto,
      ),
      decoration: new BoxDecoration( // 约束
        color: Colors.red[400],
      ),
      padding: new EdgeInsets.all(16.0),
      width: 240.0, //max-width is 240.0
    ),
  ),
  width: 320.0, // 如果想要撑满屏幕，那么宽度可以设置为超越屏幕的宽度，宽度会自动缩小到屏幕大小
  height: 240.0,
  color: Colors.grey[300],
);
```

## 控制位置和大小
### 设置绝对位置
要将widget的绝对位置指定为x-y坐标，请将其嵌套在Positionedwidget中， 该widget又嵌套在Stack widget中。
```dart
var container = new Container( // 容器盒子 列表
  child: new Stack(
    children: [
      new Positioned( // 设置一个定位盒子
        child:  new Container(
          child: new Text(
            "Lorem ipsum",
            style: bold24Roboto,
          ),
          decoration: new BoxDecoration(
            color: Colors.red[400],
          ),
          padding: new EdgeInsets.all(16.0),
        ),
        left: 24.0,
        top: 24.0,
      ),
    ],
  ), 
  width: 320.0,
  height: 240.0,
  color: Colors.grey[300],
);
```

### 旋转组件 缩放组件
要旋转一个widget，将它嵌套在一个Transform中。 使设置其alignment和origin属性分别以相对和绝对值指定变换原点。
对于简单的2D旋转，widget在Z轴上旋转。（度数×π/ 180）
```dart
var container = new Container( // div
  child: new Center( // 居中盒子
    child:  new Transform( // 旋转组件
      child:  new Container( // div
        child: new Text(
          "Lorem ipsum", // 文字
          style: bold24Roboto, // 样式集
          textAlign: TextAlign.center, // 文本对齐
        ),
        decoration: new BoxDecoration( // 背景色
          color: Colors.red[400],
        ),
        padding: new EdgeInsets.all(16.0), // padding
      ),
      alignment: Alignment.center, // 旋转原点 transform-origin
      transform: new Matrix4.identity() // 旋转参数
        ..rotateZ(15 * 3.1415927 / 180) // z轴旋转角度 .. 是dart的特殊语法，相当于js的链式调用 后面不能跟逗号
        ..scale(1.5), // 旋转
    ), 
  ),
  width: 320.0,
  height: 240.0,
  color: Colors.grey[300], // 背景色
);
```
### 线性渐变
要将线性渐变应用于widget的背景，请将其嵌套到Containerwidget中。 然后使用Container的decoration属性来创建BoxDecoration对象， 并使用BoxDecoration的gradient属性来转换背景填充。
渐变“角度”基于Alignment（x，y）值:
    如果开始和结束的x值相等，则渐变是垂直的（0° 	180°）。
    如果开始和结束的y值相等，则渐变为水平（90° 	270°）

```dart
var container = new Container( // grey box
  child: new Center(
    child: new Container( // red box
      child: new Text(
        "Lorem ipsum",
        style: bold24Roboto,
      ),
      decoration: new BoxDecoration(
        gradient: new LinearGradient( // 渐变属性
          // 垂直渐变
          begin: const Alignment(0.0, -1.0), // 渐变“角度” 垂直渐变
          end: const Alignment(0.0, 0.6),
          // 水平渐变
          // begin: const Alignment(-1.0, 0.0),
          // end: const Alignment(0.6, 0.0),
          colors: <Color>[
            const Color(0xffef5350),
            const Color(0x00ef5350)
          ],
        ),
      ), 
      padding: new EdgeInsets.all(16.0),
    ),
  ),
  width: 320.0,
  height: 240.0,
  color: Colors.grey[300],
);
```

## 处理形状
### 圆角 阴影
要给矩形添加圆角请使用BoxDecoration对象的borderRadius属性 。 创建一个新的BorderRadius对象，给该对象指定一个的半径（会四舍五入）。
在CSS中，您可以使用box-shadow属性来快速指定阴影偏移和模糊。
在Flutter中，每个属性和值都单独指定。使用BoxDecoration的boxShadow属性创建BoxShadow列表。 您可以定义一个或多个BoxShadow，它们可以叠加出自定义阴影深度、颜色等。
```dart
var container = new Container( // grey box
  child: new Center(
    child: new Container( // red circle
      child: new Text(
        "Lorem ipsum",
        style: bold24Roboto,
      ),
      decoration: new BoxDecoration(
        color: Colors.red[400], // 背景色
        borderRadius: new BorderRadius.all( // 圆角
          const Radius.circular(8.0), // 圆角
          // const Radius.elliptical(10, 30), 椭圆
        ),
        // 分别设置左右圆角
        // borderRadius: new BorderRadius.horizontal(
        //    left: Radius.circular(15.0),
        //    right: Radius.circular(10.0),
        // )
        // 分别设置上下圆角
        // borderRadius: new BorderRadius.vertical(
        //    top: Radius.circular(20.0),
        //    bottom: Radius.circular(10.0)
        // ),
        // 阴影
        boxShadow: <BoxShadow>[
            new BoxShadow (
            color: const Color(0xcc000000),
            offset: new Offset(0.0, 2.0),
            blurRadius: 4.0,
            ),
            new BoxShadow (
            color: const Color(0x80000000),
            offset: new Offset(0.0, 6.0),
            blurRadius: 20.0,
            ),
        ],
      ),
      padding: new EdgeInsets.all(16.0),
    ),
  ),
  width: 320.0,
  height: 240.0,
  color: Colors.grey[300],
);
```
### 圆和椭圆
在CSS中制作一个圆需要将矩形的四条边的border-radius设置为50%。

虽然BoxDecoration的borderRadius属性支持此方法,但Flutter为此提供了一个shape属性,值为BoxShape枚举.
```dart
var container = new Container( // grey box
  child: new Center(
    child: new Container( // red circle
      child: new Text(
        "Lorem ipsum",
        style: bold24Roboto,
        textAlign: TextAlign.center, 
      ),
      decoration: new BoxDecoration(
        color: Colors.red[400],
        shape: BoxShape.circle, // shape 专门设置圆和椭圆属性
      ),
      padding: new EdgeInsets.all(16.0),
      width: 160.0,
      height: 160.0, 
    ),
  ),
  width: 320.0,
  height: 240.0,
  color: Colors.grey[300],
);
```

## 操作文本
### 调整文本间距 转换文本 省略号
在CSS中，通过分别给出letter-spacing和word-spacing属性的长度值，指定每个字母或单词之间的空白间距。长度单位可以是px，pt，cm，em等。

在Flutter中，您将空白区域指定为Text的TextStyle的letterSpacing和wordSpacing属性， 值为逻辑像素（允许为负值）

```dart
var container = new Container( // grey box
  child: new Center(
    child: new Container( // red box
      child: new Text(
        "Lorem ipsum".toUpperCase(), // 转换为大写
        style: new TextStyle(
          color: Colors.white,
          fontSize: 24.0,
          fontWeight: FontWeight.w900,
          letterSpacing: 4.0, // 字符间距
          wordSpacing: 4.0 // 单词间距
        ),
        overflow: TextOverflow.ellipsis, // 文本超出显示省略号
        maxLines: 1, // 最大多少行视为超出
      ),
      decoration: new BoxDecoration(
        color: Colors.red[400],
      ),
      padding: new EdgeInsets.all(16.0),
    ),
  ),
  width: 320.0,
  height: 240.0,
  color: Colors.grey[300],
);
```

### 内联格式更改
Text widget控件，可以用相同的格式显示文本。 要显示使用多个样式的文本（在本例中为带有重点的单个单词），请改用RichText。 它的text属性可以指定一个或多个可单独设置样式的TextSpanwidget。

在以下示例中，“Lorem”位于具有默认（继承）文本样式的TextSpan小部件中，“ipsum”位于具有自定义样式的单独TextSpan中。
```html
<div class="greybox">
  <div class="redbox">
    Lorem <em>ipsum</em> 
  </div>
</div>
```
相当于：
```dart 
var container = new Container( // grey box
  child: new Center(
    child: new Container( // red box
      child:  new RichText(
        text: new TextSpan(
          style: bold24Roboto,
          children: <TextSpan>[
            new TextSpan(text: "Lorem "),
            new TextSpan(
              text: "ipsum",
              style: new TextStyle(
                fontWeight: FontWeight.w300,
                fontStyle: FontStyle.italic,
                fontSize: 48.0,
              ),
            ),
          ],
        ),
      ), 
      decoration: new BoxDecoration(
        backgroundColor: Colors.red[400],
      ),
      padding: new EdgeInsets.all(16.0),
    ),
  ),
  width: 320.0,
  height: 240.0,
  color: Colors.grey[300],
);
```