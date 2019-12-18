---
title: RxJs:关于异步场景的实践与探索
tags:
  - null
categories: RxJs
abbrlink: 165656931
date: 2019-12-12 23:20:47
---

# 前言

世界在进步，宣扬了这么多年的前端金科玉律被推翻，
前端飞速发展，而我们只有保持不断学习，才能不那么迷茫

而异步编程又是前端的重中之重，这里大家介绍一种新颖的前端的异步编程方法

# 关于异步

> 异步是一个关于现在和将来的问题，现在执行的代码和将来执行的代码。

关于现阶段的异步编程的方法：
* 回调函数
* 事件监听/观察者
* Promise
* Generator
* async/await
* Observable(RxJs)

### 回调函数
```js
moveTo(100, 0, function () {
    moveTo(200, 0, function () {
        moveTo(300, 0, function () {
            moveTo(400, 0, function () {
                // 无限不循环
            })
        })
    })
})
```
* 违反直觉
* 错误追踪
* 模拟同步
* 回调地狱
* 并发执行

### 事件监听/观察者

观察者模式需要一个pub和sub函数;
回调是因为观察者模式，而不是异步。其实回调的问题，观察者模式并没有解决

### Promise

```js
moveTo(100, 0).then(function () {
    return moveTo(200, 0)
}).then(function () {
    return moveTo(300, 0)
}).then(function () {
    return moveTo(400, 0)
}).then(function () {
    // 无限不循环
})

function moveTo(x = 0, y = 0) {
    return new Promise()
}
```
Promise解决了回调的一些问题，但并没有全部解决，
比如Promise有很好的错误追踪，避免了回调地狱，对并发执行很友好，因为Promise只决议一次，就很好的解决了信任问题
但是Promise丢失了语义。

### Generator 生成器

Generator是一个革命性特性，es2015(es6)中引入，让原本必须一次执行完毕函数，现在可以在中间暂停，并在下次继续执行，这就让js可以模拟协程的概念

```js
function* gen() {
    yield moveTo(100, 0)
    yield moveTo(200, 0)
    yield moveTo(300, 0)
    yield moveTo(400, 0)
    // 无限不循环
}
```

生成器能很好的解决Promise不能解决的问题，如违反直觉，繁琐的代码，
但也有其自身的问题，比如需要一个启动器来启动，需要手动控制整个流程

### async/await

```js
async function run() {
    let data = await fetch('/user')
}
```
async/await堪称完美，但是缺少复杂功能的封装，一切都需要自己实现

### Observable(RxJs)

Observable是观察者模式和迭代器模式的结合
它有3个生命周期：
* 发出next事件，可以**继续**发出事件
* 发出error事件，Observable生命结束，不能继续发出事件
* 发出completed事件，Observable生命结束，不能继续发出事件

配合RxJs的操作符，它几乎可以适用任何异步场景

下面来着重介绍一下RxJs

# RxJs 是什么


* RxJS 用于 JavaScript 的 ReactiveX 库。
* RxJS 是一个库，它通过使用 observable 序列来编写异步和基于事件的程序。
* 它提供了一个核心类型 Observable，通过操作符可以把异步事件作为集合来处理。
> RxJS 是使用 Observables 的响应式编程的库，它使编写异步或基于回调的代码更容易。

## 核心概念
* Observable (可观察对象)：表示一个概念，这个概念是一个可调用的未来值或事件的集合。
* Observer (观察者)：一个回调函数的集合，它知道如何去监听由 Observable 提供的值。
* Subscription (订阅)：表示 Observable 的执行，主要用于取消 Observable 的执行。
* Operators (操作符)： 采用函数式编程风格的纯函数 (pure function)，使用像 map、filter、concat、flatMap 等这样的操作符来处理集合。
* Subject (主体)： 相当于 EventEmitter，并且是将值或事件多路推送给多个 Observer 的唯一方式。
* Schedulers (调度器)：用来控制并发并且是中央集权的调度员，允许我们在发生计算时进行协调，例如 setTimeout 或 requestAnimationFrame 或其他。

## Observable 可观察对象

> Observables 是多个值的惰性推送集合

大家都比较熟悉观察者模式，是由两种角色构成的：数据生产者 (Producer）与数据消费者 (Consumer)

| 生产者 | 消费者 |
| ---- | ---- |
| 拉取 | 被动的: 当被请求时产生数据。	主动的: 决定何时请求数据。|
| 推送 | 主动的: 按自己的节奏产生数据。	被动的: 对收到的数据做出反应。|

那么Observable和我们熟知的观察者模式有什么不同呢
RxJS 中的Observable 一个新的 JavaScript 推送体系。Observable 是多个值的生产者，并将值“推送”给观察者(消费者)。

* Function 是惰性的评估运算，调用时会同步地返回一个单一值。
* Generator 是惰性的评估运算，调用时会同步地返回零到(有可能的)无限多个值。
* Promise 是最终可能(或可能不)返回单个值的运算。
* Observable 是惰性的评估运算，它可以从它被调用的时刻起同步或异步地返回零到(有可能的)无限多个值。

```js
import {Observable} from "rxjs";

// 创建
let stream$ = new Observable(function(observer) { // observer 是迭代器
    observer.next(1); // 发送一个值
    observer.next(2);
    setTimeout(() => {
        observer.next(3);
    });
    observer.error('error'); // 发出一个错误
    observer.complete(); // 结束这个观察者
});

// 订阅
stream$.subscribe((val) => { 
    console.log(val); // 1 2 3
});

// 清理
stream$.unsubscribe()
```

## Subscription 订阅

> Subscription 是表示可清理资源的对象，通常是 Observable 的执行。Subscription 有一个重要的方法，即 unsubscribe，它不需要任何参数，只是用来清理由 Subscription 占用的资源。

```js
import {interval} from "rxjs";

let observable = interval(1000);
let subscription = observable.subscribe(x => console.log(x));
// 稍后：
// 这会取消正在进行中的 Observable 执行
// Observable 执行是通过使用观察者调用 subscribe 方法启动的
subscription.unsubscribe();
```

## Subject 主体

> Subject 是一种特殊类型的 Observable，它允许将值多播给多个观察者，所以 Subject 是多播的，而普通的 Observables 是单播的(每个已订阅的观察者都拥有 Observable 的独立执行)
> Subject 像是 Observable，但是可以多播给多个观察者。Subject 还像是 EventEmitters，维护着多个监听器的注册表。
> 每个 Subject 都是 Observable。
> 每个 Subject 都是观察者。

```js
import {Subject} from 'rxjs'

let subject = new Subject();

subject.subscribe({
  next: (v) => console.log('observerA: ' + v)
});
subject.subscribe({
  next: (v) => console.log('observerB: ' + v)
});

subject.next(1);
subject.next(2);

// 输出
// observerA: 1
// observerB: 1
// observerA: 2
// observerB: 2
```

## Scheduler 调度器

什么是调度器？ - 调度器控制着何时启动 subscription 和何时发送通知。它由三部分组成：

* 调度器是一种数据结构。 它知道如何根据优先级或其他标准来存储任务和将任务进行排序。
* 调度器是执行上下文。 它表示在何时何地执行任务(举例来说，立即的，或另一种回调函数机制(比如 setTimeout 或 process.nextTick)，或动画帧)。
* 调度器有一个(虚拟的)时钟。 调度器功能通过它的 getter 方法 now() 提供了“时间”的概念。在具体调度器上安排的任务将严格遵循该时钟所表示的时间。

> 调度器可以让你规定 Observable 在什么样的执行上下文中发送通知给它的观察者。
调度器通常作为操作符的最后一个参数

一句话总结：改变了Observable的执行时间顺序

内置调度器一般分为4种

| 调度器 | 目的 |
| ---- | ---- |
| null | 不传递任何调度器的话，会以同步递归的方式发送通知。用于定时操作或尾递归操作。|
| Rx.Scheduler.queue | 当前事件帧中的队列调度(蹦床调度器)。用于迭代操作。|
| Rx.Scheduler.asap | 微任务的队列调度，它使用可用的最快速的传输机制，比如 Node.js 的 process.nextTick() 或 Web Worker 的 MessageChannel 或 setTimeout 或其他。用于异步转换。|
| Rx.Scheduler.async | 使用 setInterval 的调度。用于基于时间的操作符。|

# RxJs 可以解决什么问题

## 统一了数据来源
RxJs 最大的特点就是可以把所有的事件封装成一个 Observable，翻译过来就是可观察对象。只要订阅这个可观察对象，就可以获取到事件源所产生的所有事件。
想象一下，所有的 DOM 事件、ajax 请求、WebSocket、数组等等数据，统统可以封装成同一种数据类型。
这就意味着，对于有多个来源的数据，我们可以每个数据来源都包装成 Observable，统一给视图层去订阅，这样就抹平了数据源的差异。

## 强大的异步同步处理能力RxJs 
提供了功能非常强大且复杂的操作符（ Operator） 用来处理、组合 Observable，几乎可以满足任何异步逻辑的需求，它抹平了同步和异步之间的鸿沟，

## 数据推送的机制把拉取的操作变成了推送的操作
RxJs 传递数据的方式和传统的方式有很大不同，那就是改“拉取”为“推送”。
原本一个组件如果需要请求数据，那它必须主动去发送请求才能获得数据，这称为“拉取”。如果像 WebSocket 那样被动地接受数据，这称为“推送”。
如果这个数据只要请求一次，那么采用“拉取”的形式获取数据就没什么问题。但是如果这个数据之后需要更新，那么“拉取”就无能为力了，开发者不得不在代码里再写一段代码来处理更新。
但是 RxJs 则不同。RxJs 的精髓在于推送数据。组件不需要写请求数据和更新数据的两套逻辑，只要订阅一次，就能得到现在和将来的数据。这一点改变了我们写代码的思路。
我们在拿数据的时候，还需要考虑未来的数据何时获取、如何获取。如果不考虑这一点，就很难开发出具备实时性的应用。如此一来，就能更好地解耦视图层和数据层的逻辑。视图层从此不用再操心任何有关获取数据和更新数据的逻辑，只要从数据层订阅一次就可以获取到所有数据，从而可以只专注于视图层本身的逻辑。

## BehaviorSubject 可以缓存数据。
BehaviorSubject 是一种特殊的 Observable。如果 BehaviorSubject 已经产生过一次数据，那么当它再一次被订阅的时候，就可以直接产生上次所缓存的数据。

# RxJs 的经典场景

### 防抖：操作符 debounce/debounceTime

简单防抖
```js
import { fromEvent, interval, } from 'rxjs';
import { debounceTime } from 'rxjs/operators';

const clicks = fromEvent(document, 'click');
const result = clicks.pipe(debounceTime(1000));
result.subscribe(x => console.log(x));
```

自定义防抖时机，如果当从服务器请求值后才进行触发
```js
import { fromEvent, interval } from 'rxjs';
import {fromPromise} from "rxjs/internal-compatibility";
import { debounce } from 'rxjs/operators';

const clicks = fromEvent(document, 'click');
const result = clicks.pipe(debounce(() => fromPromise(fetch('/check_name'))));
result.subscribe(x => console.log(x));
```

### 节流：操作符 throttle
每个1秒接受一次点击事件
```js
import { fromEvent } from 'rxjs';
import { throttle } from 'rxjs/operators';

const clicks = fromEvent(document, 'click');
const result = clicks.pipe(throttle(ev => interval(1000)));
result.subscribe(x => console.log(x));
```
鼠标移动时每隔一帧渲染一次
```js
import { fromEvent } from 'rxjs';
import { throttle } from 'rxjs/operators';

const clicks = fromEvent(document, 'mousemove');
const result = clicks.pipe(throttle(ev => interval(16)));
result.subscribe(x => console.log(x));
```

### 过滤：操作符 filter/distinct/distinctUntilChanged/distinctUntilKeyChanged

只发出目标是 DIV 元素的点击事件
```typescript
import { of, fromEvent } from 'rxjs';
import { filter } from 'rxjs/operators';

const clicks$ = fromEvent(document, 'click');
const clicksOnDivs = clicks$.filter(ev => ev.target.tagName === 'DIV');
clicksOnDivs.subscribe(x => console.log(x));
```

过滤列表中重复的项
```js
import { of } from 'rxjs';
import { distinct } from 'rxjs/operators';

of(1,1,3,3,4,4,5).pipe(distinct()).subscribe(x => console.log(x));
// 输出：1 3 4 5
```
```typescript
import { of } from 'rxjs';
import { distinct, distinctUntilChanged, distinctUntilKeyChanged } from 'rxjs/operators';

interface Person {
   age: number,
   name: string
}
let list$ = of<Person>(
    { age: 4, name: 'Foo'},
    { age: 7, name: 'Bar'},
    { age: 5, name: 'Foo'},
  )
```

```typescript
list$.pipe(
    distinct((p: Person) => p.name),
  )
  .subscribe(x => console.log(x));

// 输出:
// { age: 4, name: 'Foo' }
// { age: 7, name: 'Bar' }
```

还可以只与上一次的值不同
```typescript
list$.pipe(
    distinctUntilChanged((p: Person, q: Person) => p.name === q.name),
  )
  .subscribe(x => console.log(x));

// 输出
// { age: 4, name: 'Foo' }
// { age: 7, name: 'Bar' }
// { age: 5, name: 'Foo' }
```
还可以再直观一点,比较名字前三个字母
```typescript
list$.pipe(
    distinctUntilKeyChanged('name', (x: string, y: string) => x.substring(0, 3) === y.substring(0, 3)),
  )
  .subscribe(x => console.log(x));

// displays:
// { age: 4, name: 'Foo1' }
// { age: 7, name: 'Bar' }
// { age: 5, name: 'Foo2' }
```

### once只触发一次：操作符 elementAt
当用户点击第三次的时候触发，并卸载当前订阅
```typescript
import { fromEvent } from 'rxjs';
import { elementAt } from 'rxjs/operators';

const clicks = fromEvent(document, 'click');
const result = clicks.pipe(elementAt(2));
result.subscribe(x => console.log(x));

// click 1 = nothing
// click 2 = nothing
// click 3 = MouseEvent object logged to console
```

# RxJs 的复杂场景

这是我第三次学习RxJs了，RxJs的的学习曲线比较陡峭，但是确实受益匪浅
RxJs改变了我思考问题的方式，它让我思考问题的方式更全面，更完整
在最近的项目中更是有着不同想法，设想如果以RxJs的模式去组织代码可以得到怎么样的收益

## 场景1：当被订阅时才进行事件绑定

关于事件绑定，前端开发者大概都会这样写：
1 提前选取Element，
2 绑定事件，并emit事件
3 在组件卸载的同时卸载事件

其实这样写是完全没有问题的，但是如果这个组件使用的功能比较浅，那么可能就不需要去监听事件，组件中Element上绑定的事件就浪费了内存与性能！
如果站在RxJs的角度怎么思考呢：我们可以参考 Observable，Observable 是惰性的，那么我们设计组件的时候也可以是惰性的，
当组件初始化的时候检查一下是否绑定了事件，如果绑定了就在组件内部挂载一下事件，这样就可以有选择的去监听，不需要去做无意义的事件触发

伪代码

```
let $listeners = this.$listeners // 得到绑定的事件列表

let ob$ = new Observable(function(observe) {
    observe.next(value)
})

```

## 场景2：ajax请求错误重试

### 非 RxJs 的方式
在我们没有接触 RxJs 时，请求的数据如果出错需要重试的时候我们可能会这样组织代码：

简单的组织需要一个递归函数来重新请求
```
function ajaxAgain() {
    axios.get().then().catch(err=>ajaxAgain())
}
```

如果需要限制最大次数那么需要一个变量来保持次数

```
function ajaxAgain(maxCount, count) {
    if (!count) { count = 0 }
    if (count>maxCount) return
    axios.get().then().catch(err=>ajaxAgain(maxCount, count++))
}
```

如果需要判断权限或者更复杂的操作时

```
function ajaxAgain(maxCount, count) {
    if (!count) { count = 0 }
    if (count>maxCount) return
    if (any) {
        // do something
    }
    axios.get().then().catch(err=>ajaxAgain(maxCount, count++))
}
```

### 使用**RxJs**组织代码

如果只想重试3次
```js
import {timer} from "rxjs";
import {ajaxGet} from "rxjs/internal-compatibility";
import {retry, retryWhen, take, mergeMap} from 'rxjs/operators';

ajaxGet('').pipe(retry(3)).subscribe(() => {})
```

如果希望每次延迟2秒再重试
```
ajaxGet('/user').pipe(retryWhen(() => timer(2000))).subscribe(() => {})
```
如果希望每次延迟2秒再重试，并尝试3次
```
ajaxGet('/user').pipe(retryWhen(() => interval(2000).pipe(take(3)))).subscribe(() => {})
```
如果请求失败，去另外一个接口询问是否需要重试
```
ajaxGet('/user').pipe(retryWhen(() => ajaxGet('/check'))).subscribe(() => {})
```

如果请求失败，2秒后再去另外一个接口询问是否需要重试
```
ajaxGet('/user').pipe(retryWhen(() => {
  return timer(2000).pipe(mergeMap(() => ajaxGet('/check')))
})).subscribe(() => {})
```

直观简单了不少，复杂逻辑被操作符处理，我们只关心业务即可

## 场景3：数字动画
```
fromEvent(updateButton, 'click').pipe(
    map(_ => parseInt(input.value)),
    switchMap(endRange => {
      return timer(0, 20).pipe(
        mapTo(positiveOrNegative(endRange, currentNumber)),
        startWith(currentNumber),
        scan((acc, curr) => acc + curr),
        takeWhile(takeUntilFunc(endRange, currentNumber));
      )
    }),
    tap(v => (currentNumber = v)),
    startWith(0)
)
```





# RxJs 的基本用法

## 创建 Observable

### interval 定时器

```typescript
import {Observable, interval} from 'rxjs';

const stream$: Observable<any> = interval(1000);
stream$.subscribe((count: number) => {
  console.log(count); // 每隔1000毫秒输出 0 1 2 3 ...
});
```

### timer 延时触发的定时器
```typescript
import {Observable, timer} from 'rxjs';

const timer$: Observable<any> = timer(1000);
timer$.subscribe(() => {
  // 1000毫秒之后触发
});
```


```typescript
import {Observable, timer} from 'rxjs';

const timer$: Observable<any> = timer(1000, 100);
timer$.subscribe(() => {
  // 1000毫秒之后触发，每隔100毫秒触发 0 1 2 3 
});
```

### defer 延迟创建
> 有时候我们希望Observable不要太早创建，另一方面我们又希望Observable尽可能的早创建，这个是一个矛盾的需求，解决这个矛盾需求的方式，就是依然创建一个Observable。
> 但是这个Observable只是一个代理(Proxy),在创建之时并不会做分配资源的工作，
> 只有被订阅的时候，才会去创建真正占用资源的Observable，之前产生的代理Observable会把所有工作都转交给真正占用资源的Observable

defer允许你创建一个 Observable 当且仅当它被订阅的时候，并且为每个订阅者创建新的 Observable

```typescript
import {defer, of} from 'rxjs';

const of$ = of(1, 2, 3); // 
const defer$ = defer(() => of$);

```

### fromPromise 将promise转化为Observable

```typescript
import {fromPromise} from 'rxjs/internal-compatibility';

const promise$ = fromPromise(new Promise(((resolve, reject) => {
  if (Math.random() > 0.5) {
    resolve('大于0.5');
  } else {
    reject('小于0.5');
  }
})));

promise$.subscribe((res) => {
  console.log(res);
}, error => {
  console.log('被拒绝', error);
});

```
```typescript
import {fromPromise} from 'rxjs/internal-compatibility';

fromPromise(fetch('/users'));
```

### fromEvent 订阅事件

```typescript
import {Observable, fromEvent} from "rxjs";

let event$: Observable<any> = fromEvent(document.body, 'click')
event$.subscribe((e: Event) => {
  console.log('事件流', e)
})

```

### fromEventPattern 自定义定义事件与移除事件

```typescript
import {fromEventPattern, Subscription} from "rxjs";

function addClickHandler(handler) {
  console.log('创建监听')
  document.addEventListener('click', handler);
}

function removeClickHandler(handler) {
  console.log('移除监听')
  document.removeEventListener('click', handler);
}

let clicks = fromEventPattern(
  addClickHandler,
  removeClickHandler
);
const click$: Subscription = clicks.subscribe(x => console.log(x));
setTimeout(() => {
  click$.unsubscribe()
}, 1000)
```

## 转换 

### map 将当前值映射为另一个值

```typescript
import {fromEvent} from "rxjs";
import {map} from 'rxjs/operators';

let clicks$ = fromEvent(document, 'click');
let positions$ = clicks$.pipe(map(ev => ev.clientX));
positions$.subscribe(x => console.log(x));
```

### pluck map的快捷方式

```typescript
import {fromEvent} from "rxjs";
import {pluck} from 'rxjs/operators';

let clicks$ = fromEvent(document, 'click');
let positions$ = clicks$.pipe(pluck('target', 'tagName'));
positions$.subscribe(x => console.log(x));
```

### scan 累加器，类似于reduce

```typescript
import { fromEvent } from 'rxjs';
import { scan, mapTo } from 'rxjs/operators';

const clicks = fromEvent(document, 'click');
const ones = clicks.pipe(mapTo(1));
const seed = 0;
const count = ones.pipe(scan((acc, one) => acc + one, seed));
count.subscribe(x => console.log(x));
```

### pairwise 将发出的值每两个分一组

```typescript
import { fromEvent } from 'rxjs';
import { pairwise, map } from 'rxjs/operators';

const clicks = fromEvent(document, 'click');
const pairs = clicks.pipe(pairwise());
const distance = pairs.pipe(
  map(pair => {
    const x0 = pair[0].clientX;
    const y0 = pair[0].clientY;
    const x1 = pair[1].clientX;
    const y1 = pair[1].clientY;
    return Math.sqrt(Math.pow(x0 - x1, 2) + Math.pow(y0 - y1, 2));
  }),
);
distance.subscribe(x => console.log(x));
```

### buffer / bufferCount / bufferTime / bufferToggle

#### buffer 
> 从过去收集值作为一个数组，并且仅在另一个Observable发出时才发出该数组。

每次点击页面输出定时器走了多少次
```typescript
import { fromEvent, interval } from 'rxjs';
import { buffer } from 'rxjs/operators';

let clicks$ = fromEvent(document, 'click');
let interval$ = interval(1000);
let buffered = interval$.pipe(buffer(clicks$));
buffered.subscribe(x => console.log(x));
// [0, 1]
// [2]
// [3, 4]
```

#### bufferCount
> 缓存发出的值，知道达到制定的最大值

每点击两次输出 [MouseEvent, MouseEvent]
```typescript
import { fromEvent } from 'rxjs';
import { bufferCount } from 'rxjs/operators';

let clicks$ = fromEvent(document, 'click');
let bufferCount$ = clicks$.pipe(bufferCount(2))
bufferCount$.subscribe(x => console.log(x));


```

#### bufferTime 
> 在特定时间段内缓冲源Observable值。

每秒发出一系列最近的点击事件
```typescript
import { fromEvent } from 'rxjs';
import { bufferTime } from 'rxjs/operators';

const clicks = fromEvent(document, 'click');
const buffered = clicks.pipe(bufferTime(1000));
buffered.subscribe(x => console.log(x));
```

每隔5秒，从接下来的2秒发出点击事件
```typescript
import { fromEvent } from 'rxjs';
import { bufferTime } from 'rxjs/operators';

const clicks = fromEvent(document, 'click');
const buffered = clicks.pipe(bufferTime(2000, 5000));
buffered.subscribe(x => console.log(x));
```

#### bufferToggle

每隔一秒钟，从接下来的500ms发出点击事件
```typescript
import { fromEvent, interval, EMPTY } from 'rxjs';
import { bufferToggle } from 'rxjs/operators';

const clicks = fromEvent(document, 'click');
const openings = interval(1000);
const buffered = clicks.pipe(bufferToggle(openings, i =>
  i % 2 ? interval(500) : EMPTY
));
buffered.subscribe(x => console.log(x));
```

# 其他语言实现

* [RxJava](https://github.com/ReactiveX/RxJava)
* [RxGo](https://github.com/ReactiveX/RxGo)
* [RxPython](https://github.com/ReactiveX/RxPY)
* [RxPHP](https://github.com/ReactiveX/RxPHP)
* [RxSwift](https://github.com/ReactiveX/RxSwift)
* [RxAndroid](https://github.com/ReactiveX/RxAndroid)
* [RxDart](https://github.com/ReactiveX/rxdart)
* [RxCpp](https://github.com/ReactiveX/RxCpp)

# 参考文献

[RxJs(5)中文官网](https://cn.rx.js.org/)
[RxJs(6)官网](https://rxjs.dev/)
[用动画的方式演示了大部分Rxjs的Operator的执行过程](https://reactive.how)
