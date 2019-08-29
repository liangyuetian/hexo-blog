---
title: 过滤操作符
tags:
  - rxjs
  - 操作符
categories: RxJs
playlist:
  - name: Apple.com（Cover miku）
    artist: Hanser
    url: //music.163.com/song/media/outer/url?id=497215230.mp3
    cover: >-
      //p2.music.126.net/9GAbSb_hlXPu66HWInJOww==/109951162846052486.jpg?param=90y90
abbrlink: 3458632642
date: 2019-01-03 22:34:58
---

<img src="//p2.music.126.net/9GAbSb_hlXPu66HWInJOww==/109951162846052486.jpg?param=90y90" width = "100" height = "100" div align=right style="position: absolute; right: 0; margin-top: -10px;" />
filter，first，last，take，takeLast
takeWhile,takeUntil,
skip,
skipWhile,skipWhile
throttleTime,....

<!-- more -->

### filter 过滤操作符
> 过滤掉不满足判定条件的数据

### first 第一个数据
> 获得满足判定条件的第一个数据

### last
> 获得满足条件的最后一个数据

### take
> 从数据流中选取最先出现的若干数据

### takeLast
> 从数据流中选取最后出现的若干数据

### takeWhile takeUntil
> 从数据流中选取数据直到某种情况发生

### skip
> 从数据流中忽略最先出现的若干数据

### skipWhile skipUntil
> 从数据流中选取数据直到某种情况发生

### throttleTime
> throttleTime的作用是：限制在duration时间范围内，从上游传递给下游数据的个数。也就是说，多少毫秒发送一个值给下游
```js
const result$ = Observable.interval(1000).throttleTime(2000)
// 意味着2000毫秒之内只有一个数据会传给下游
```

### debounceTime
> debounceTime的作用是：让传递给下游的数据间隔不能小于给定的时间dueTime。也就是说，经过了dueTime时间没有产生数据，那么触发下游数据！
```js
const result$ = Observable.interval(1000).debounceTime(2000)
// 意味着2000毫秒之内没有产生数据，才把当前数据传给下游
```

### throttle 用数据控制流量 多长时间返回下一个数据
```js
const source$ = Observable.interval(1000)
const durationSelector = value => {
    console.log(value)
    return Observable.timer(2000)
}
source$.throttle(durationSelector)

// 输出0 2 4
```
每隔2000毫秒输出一个结果，即使用durationSelector来控制数据流与不流的阀门，实际操作用，durationSelector可以换成ajax等异步操作

### debounce 用数据控制流量 在多长时间的间隔内不返回数据
```js
const source$ = Observable.interval(1000)
const durationSelector = value => {
    console.log(value)
    return Observable.timer(value % 3 === 0 ? 2000 : 1000)
}
source$.debounce(durationSelector)

// 输出 1 2   4 5   6 7 
```
3的倍数因为延时2000毫秒而被抛弃

### auitTime 取时间段的最后一个数据,与throttleTime相似
```js
const source$ = Observable.interval(1000)
const result$ = source$.auditTime(2000)
// 输出：1 3 5
```
与throttleTime相似，throttleTime取时间段内第一个数据，auditTime取时间段的最后一个数据

### sampleTime sample 采样数据
sampleTime 不管上游source$产生数据的节奏如何，完全根据自己参数指定的好描述间隔节奏来给下游传递数据。
节奏与和上游数据完全无关，而auditTime是根据上游数据触发的
```js
const source$ = Observable.interval(500).take(2).mapTo('A')
    .concat(Observable.interval(1000).take(3).mapTo('B'))
    .concat(Observable.interval(500).take(3).mapTo('C'))
const result$ = source$.sampleTime(800)
```
注意：如果sampleTime发现一个时间块内上游没有产生数据，那在时间块结尾也不会给下游传递数据。

### distinct 只返回从没有出现过的数据
```js
const source$ = Observable.of(0, 1, 1, 2, 2, 0, 0, 1, 3, 3)
source$.distinct()
// 输出：0 1 2 3 complete
```
distinct 提供了一个函数参数keySelector，用于定制distinct应该对比什么样属性
```js
const source$ = Observable.of({}, {}, {})
source$.distinct(x => x.name)
```
为了防止内存泄漏(产生大量不同数据),distinct提供第二个可选的参数flush,
第二个参数可以是一个Observable对象，每当Observable对象产生数据是，distinct就清空‘唯一数据集合’,一切重来，这样就避免了内存泄漏
```js
const source$ = Observable.of({}, {}, {})
source$.distinct(null, Observable.interval(500))
// 这个值是什么不重要，重要的是它会清空distinct以前积压的所有唯一的数据
```

### distinctUntilChanged 也是淘汰掉重复数据，但是直接跟上一个数据比较
```js
const source$ = Observable.of(0, 1, 1, 2, 2, 0, 0, 1, 3, 3)
source$.distinctUntilChange()
// 输出：0,1,2,0,1,3
```
distinctUntilChange也接受第二个参数，但是第二个参数需要返回一个bool类型的结果，(设计失误)
```js
const source$ = Observable.of({}, {}, {})
source$.distinctUntilChange((a, b) => a.name === b.name)
```

## 其他过滤方式
### ignoreElements 忽略所有的元素
```js
const source$ = Observable.interval(1000).take(5)
const result$ = source$.ignoreElements()
// 不输出任何结果
```
### elementAt 把上游数据当成数组，只获取下标的那一个数据
elementAt还有第二个参数，可以指定没有对应下班数据时的默认值
```js
const source$ = Observable.of(1, 2, 3)
const result$ = source$.elementAt(3, null)
```

### sigle 
检查上游是否只要一个满足对应条件的数据，如果答案为‘是’，就向下游传递这个数据
如果为‘否’，就向下雨传递一个异常
```js
const source$ = Observable.interval(1000).take(2)
const result$ = source$.single(x => x % 2 === 0)

// 反例
const source$ = Observable.interval(1000)
const result$ = source$.single(x => x % 2 === 0)
// source$ 会持续产生递增整数，当source$产生数据2时，single就发现source$中产生了两个偶数，这时候就会立刻向下游传递下面的错误
```
