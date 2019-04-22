---
title: 多播
tags:
  - rxjs
  - 多播
categories: RxJs
playlist:
  - name: 杏花弦外雨
    artist: CRITTY / 司夏
    url: //music.163.com/song/media/outer/url?id=32957012.mp3
    cover: >-
      //p2.music.126.net/iwZ6w7D5C8WXgnjcohHb0Q==/7799935488436943.jpg?param=90y90
abbrlink: 7684
date: 2019-01-08 23:03:58
---

<img src="//p2.music.126.net/9GAbSb_hlXPu66HWInJOww==/109951162846052486.jpg?param=90y90" width = "100" height = "100" div align=right style="position: absolute; right: 0; margin-top: -10px;" />
灵活选取Subject对象进行多播：multicast
只多播数据流中最后一个数据：publishLast
对数据流中给定数量的数据进行多播：publishReplay
拥有默认数据的多播：publishBehavior
<!-- more -->
### 多播就是让一个数据流的内容被多个Observable订阅

## 大纲
* 灵活选取Subject对象进行多播：multicast
* 只多播数据流中最后一个数据：publishLast
* 对数据流中给定数量的数据进行多播：publishReplay
* 拥有默认数据的多播：publishBehavior

在RxJs中，Observbale和Observer的关系就是前者在播放内容，后者在收听内容
播放的内容大致可以分为3种

* 单播
* 广播
* 多播

所谓单播，就是一个播放这对应一个收听者，一对一的关系
广播：一个收听者对于多个播放者，RxJs不支持广播，因为已经有很多现成的解决方案，例如：EventEmitter
```js
import EventEmitter from 'event';
const eventHub = new EventEmitter();

eventHub.on('data', info => {console.log(info)});
eventHub.emit('data', 'some data')
eventHub.emit('data', 'some other data')
```
广播的问题是，发布消息的根本不着调听众是什么样的人，于是筛选消息的责任就完全落在了接收方的身上，而且广播中容易造成频道冲突
就像无线电的共用频段
多播：一个播放者对应一个收听者
注意，clod 的Observable无法实现多播

**Hot Cold 数据流差异**
所谓Clod的数据流就是每次被subscribe都产生一个全新的数据序列的数据流
所谓Hot数据流就是无论有多少Observer来subscribe，推给Observable的都是一样的数据源，满足这一种条件的，就是Hot Observable

Hot 和 Cold 都具有懒的特质，不过Cold更懒一些，两者的数据管道内逻辑都是只有存在订阅者才会执行，
Cold 更懒体现在，如果没有订阅者，连数据都不会真正产生，对于Hot Observable 没有订阅者的情况，数据依旧产生，只不过不传入数据管道

## Subject

