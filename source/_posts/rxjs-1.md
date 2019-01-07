---
title: rxjs 简介
date: 2019-01-01 23:09:18
tags: rxjs
playlist:
  -
    name: 【bilibili音乐】一话一世界 《一花依世界》方言合唱版
    artist: 雪霏岚岚 / 早木旋子 / 张小她 / 倒悬的橘子 / 双笙 / 宁采臣丶在唱歌 / A路人 / 漆柚 / YUKIri / warma / LKs / 洛天依 / 乐正绫
    url: "//music.163.com/song/media/outer/url?id=1302601503.mp3"
    cover: "//p2.music.126.net/KuEjitsnKt05FxnoKD0xcw==/109951163466304647.jpg?param=90y90"
---

<img src="//p2.music.126.net/KuEjitsnKt05FxnoKD0xcw==/109951163466304647.jpg?param=90y90" width = "100" height = "100" div align=right style="position: absolute; right: 0; margin-top: -10px;" />
函数式编程，Observable 和 Observer 
观察者模式，迭代器模式
操作符

<!-- more -->

# RxJs简介

### 首先，函数式编程
顾名思义,函数式编程就是非常强调使用函数来解决问题的一种编程方式。
要求以下几点：
* 声明式
* 纯函数
* 数据不可变性

### Observable 和 Observer 
* observable: 可以被观察的对象，即可被观察者
* observer: 观察者

### RxJS中的数据流就是Observable对象，Observable实现了下面两种设计模式：
* 观察者模式
* 迭代器模式

### Observable 的核心在于各种各样的操作符
操作符分为以下几大类：
* 创建操作符
* 合并操作符
* 辅助类操作符
* 过滤操作符
* 转化操作符
* 异常错误处理操作符

### 还有
* 多播
* 调度器(Scheduler)
* 高阶Observable
* rxjs的调试和测试