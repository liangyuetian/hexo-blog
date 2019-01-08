---
title: 转化操作符
date: 2019-01-07 21:21:12
tags: rxjs
categories: 
playlist:
  -
    name: Secret
    artist: 茶太
    url: "//music.163.com/song/media/outer/url?id=640866.mp3"
    cover: "//p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90"
---

<img src="//p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90" width = "100" height = "100" div align=right style="position: absolute; right: 0; margin-top: -10px;" />
map, mapTo, pluck
scan mergeScan

<!-- more -->

### map 数据转化
接受一个函数作为参数，这个函数通常称为project，指定了数据映射的逻辑
第二个参数指定this
```js
const source$ = Observable.interval(1000).take(5)
const result$ = source$.map(x => x * 2, this)
// 输出 0 2 4 8 。。。
```

### mapTo 数据转化
将输出映射为另一个固定的值
```js
const source$ = Observable.interval(1000).take(5)
const result$ = source$.mapTo('A')

// 也可以用map实现
Observable.prototype.mapTo = function (value) {
    return this.map(x => value)
}
```

### pluck
pluck 的含义时拔，pluck 就是把上游数据中特定字段的值拔出来
所以pluck的参数就是字段的名字
```js
const source$ = Observable.of(
    {name: 'Rxjs'},
    {name: 'Angular'},
    {name: 'React'},
    {name: 'Vue'}
)
const result$ = source$.pluck('name')
// 输出 Rxjs Angular React Vue
```

### scan
scan 都是规约函数
sacn reduce 的区别在于sacn对上游每一个数据都会产生一个规约结果，
而reduce是对上游所有数据进行规约，
reduce最多只给下游传递一个数据吗如果上游数据永不完结，那reduce也永远不会产生数据，而scan完全可以处理一个永不完结的上游Observable对象
```js
const source$ = Observable.interval(1000)
const result$ = source$.scan(accumulation, value) => {
    return accumulation + value
})
// 每次source$触发scan规约函数都会被调用
```