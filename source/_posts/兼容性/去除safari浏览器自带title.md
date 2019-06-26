---
title: 去除safari浏览器自带title
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-06-26 11:51:33
tags: 兼容性
categories: 兼容性
---

去除safari浏览器自带title
[http://blog.sina.com.cn/s/blog_69ec42d50102ywqf.html](http://blog.sina.com.cn/s/blog_69ec42d50102ywqf.html)
[https://stackoverflow.com/questions/20974276/prevent-safari-from-showing-tooltip-when-text-overflow-is-hidden-with-ellipsis](https://stackoverflow.com/questions/20974276/prevent-safari-from-showing-tooltip-when-text-overflow-is-hidden-with-ellipsis)

<!-- more -->
safari浏览器在文本溢出时会非常智能的添加一个title用于提示体验，
但是有时候我们自定义了title，不需要自带的title时就可以使用以下2种方式来屏蔽自带title,并且不与正常的title属性冲突

### 1 空div法
```html
<div>
    <div></div>
    你是从石头里蹦出来的吧，你总是忧虑，因为几万年来沉寂的你还在害怕那林间飞速的跳跃，千百万扑面而来的事物，本篇文章来自资料管理下载。而你知道你能如此自由的掌握自己的时间是极短暂的，你能感受到自己这样思考的时间是极短暂的，为了这短暂的时光，你要尽力的去抓住你所遇见的。
</div>
```

参考来源：[http://blog.sina.com.cn/s/blog_69ec42d50102ywqf.html](http://blog.sina.com.cn/s/blog_69ec42d50102ywqf.html)

### 2 before,after法
```css
&::before {
    content: '';
    display: block;
}
```

参考来源：[https://stackoverflow.com/questions/20974276/prevent-safari-from-showing-tooltip-when-text-overflow-is-hidden-with-ellipsis](https://stackoverflow.com/questions/20974276/prevent-safari-from-showing-tooltip-when-text-overflow-is-hidden-with-ellipsis)