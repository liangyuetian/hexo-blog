---
title: 创建操作符
tags:
  - rxjs
  - 操作符
categories: RxJs
playlist:
  - name: 绝代风华
    artist: 许嵩
    url: //music.163.com/song/media/outer/url?id=1327456179.mp3
    cover: >-
      //p2.music.126.net/Ngh65GwhHtufNRSs9KgEIA==/109951163681417636.jpg?param=90y90
abbrlink: 2625071920
date: 2019-01-01 23:20:47
---

<img src="//p2.music.126.net/Ngh65GwhHtufNRSs9KgEIA==/109951163681417636.jpg?param=90y90" width = "100" height = "100" div align=right style="position: absolute; right: 0; margin-top: -10px;" />
rxjs 操作符 - 创建操作符
create，of，range，generate，repeat，empty
throw，never，interval，timer
from，fromPromise，fromEvent，fromEventPattern
ajax，defer

<!-- more -->

## 第一大类：创建操作符

| 操作符 | 功能 |
| ------ | ------ |
| create | 直接创建观察者对象 |
| of | 根据参数直接产生同步数据 |
| range | 产生一个数值范围内的数据 |
| generate | 以循环方式产生数据，相当于for循环，有判断条件 |
| repeat repeatWhen | 重复产生数据流中的数据 |
| empty | 产生空的数据流，并结束 |
| throw | 产生直接出错的数据流，并结束 |
| never | 产生永不完结的数据流 |
| interval timer | 间隔给定时间持续产生数据流 |
| from | 从数组等枚举类型数据产生数据流 |
| fromPromise | 从Promise中产生数据流 |
| fromEvent fromEventPattern | 从外部事件对象产生数据流 |
| ajax | 从ajax请求产生数据流 |
| defer | 延迟产生数据流 |

例子：
#### create 毫无特点
```js
Observable.create = function(subscribe) {
    return new Observable(subscribe)
}
```

#### of 列举数据
```js
const source$ = Observable.of(1, 2, 3);
source$.subscribe(
    console.log, // 收到数据时触发的回调
    null, // 相当于catch
    () => console.log('complete') // 当整个数据流结束时触发
) // result: 1 2 3 同步产生
```

#### range 指定范围
```js
const source$ = Observable.range(1, 100);
// 订阅后直接输出1 2 3 4 5 ... 99 100 同步产生
```

#### generate 循环创建
```js
const source$ = Observable.generate(
    2, // 初始值
    value => value < 10, // 继续的条件，相当于for中的条件判断
    value => value + 2, // 每次递增的值
    value => value * value // 产生的结果
// 使用generate四个参数分别对应了for循环中的不同表达式，其中除了第一个参数是一个值外，其余三个参数都是函数，应该保持着三个参数都是纯函数。
)
```

#### repeat 重复数据的数据流
```js
const source$ = Observable.of(1, 2, 3);
const repeated$ source$.repeat(10); // 将source$的数据产生10遍，共产生30个数据，每次循环都会经历订阅，退订的过程
```

#### 三个极简的操作符：empty never throw 
```js
const source$ = Observable.empty(); // 直接产生完结对象，没有参数，不产生任何数据
const source$ = Observable.throw(new Error('直接报错')); // 直接抛出错误，立即完结
const source$ = Observable.nerver(); // nerver 真的什么都不做，即不吐出数据，也不完结，也不产生错误，就这样待着，直到天荒地老
```
### 创建异步数据的Observable对象
#### interval timer 定时产生数据
```js 
const source$ = Observable.interval(1000); // 从0开始，每秒产生一个数据，第一数据产生在订阅1秒后
// 如果想从1开始
const result$ = source%.map(x => x + 1); // 与map组合

// timer 指定产生第一数据的延迟时间
const source$ = Observable.timer(2000); // 2秒后产生第一个数据0，然后立刻完结
const source$ = Observable.timer(2000, 1000); // 2秒后产生第一个数据0，然后接下来每隔1秒依次产生一个数据
```

#### from 可以把一切转化为Observable
```js
const source$ = Observable.from([1, 2, 3]); // 依次输出1 2 3 甚至参数可以是argument

function * generateNumber(max) {
    for(let i = 0; i <= max; ++i) {
        yield i;
    }
}
const source$ Observable.from(generateNumber(3)); // 甚至可以消化yield

const source$ = Observable.from('abc'); // 输出 a b c 
```

#### fromPromise 异步处理交接
```js
const promise = Promise.resolve('good');
const source$ = Observable.from(promise);
source$.subscribe(
    console.log,
    error => console.log('catch', error),
    () => console.log('complete')
)
```

#### fromEvent 把dom中的事件转化为Observable对象中的数据
```js
// 第一个参数是一个事件源，如dom元素，第二个参数是事件名称，对应dom事件就是click,mousemove这样的字符串
const event$ = Rx.Observable.fromEvent(document.querySelector('#clickMe'), 'click');
event$.subscribe(
    e => {
        // ...
    }
)
// 还可以使用node的events中获得数据
import EventEmitter from 'events';
const emitter = new EventMitter();
const source$ = Observable.from(emitter, 'msg');
source$.subscribe(
    console.log
);

emitter.emit('msg', 1);
eemitter.emit('msg', 2);
mitter.emit('another-mag', 3); // 别的事件
emitter.emit('msg', 4);
// 输出 1 2 4
```

#### fromEventPattern 
```js
// fromEventPattern 接受2个函数参数，分别对应产生的Observable对象被订阅和退订时的动作,因为这两个参数是函数，具体动作可以任意定义，所以可以非常灵活
const emitter = new EventMitter();
const addHandler = handler => {
    emitter.addListener('msg', handler);
}
const removeHandler = handler => {
    emitter.removeListener('msg', handler);
}
const source$ = Observable.fromEventPattern(addHandler, removeHandler);
conset subscription = source$.subscirbe( // 订阅
    () => {}
)
subscription.unsubscribe() // 退订
// 其中参数handler相当于Observable的next函数
```

#### defer 推迟占用资源的一种惯用模式
```js
// defer 推迟占用资源的一种惯用模式
// defer 接受一个函数作为参数，当defer产生的Observable对象被订阅的时候，defer的函数参数就会被调用，预期这个函数会返回另一个Observable对象，也就是defer转嫁所有工作的对象，因为Promise和Observable的关系，defer也很贴心的支持返回Promise对象的函数参数。
const observableFactory = () => Observable.of(1, 2, 3);
const source$ = Observable.defer(observaleFactory);
```
