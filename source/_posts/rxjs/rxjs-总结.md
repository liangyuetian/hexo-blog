---
title: RxJs总结
tags:
  - null
categories: RxJs
abbrlink: 165656931
date: 2019-12-12 23:20:47
---

# Rxjs 是什么
用于 JavaScript 的 ReactiveX 库。
> RxJS 是使用 Observables 的响应式编程的库，它使编写异步或基于回调的代码更容易。

[中文官网](https://cn.rx.js.org/)
[用动画的方式演示了大部分Rxjs的Operator的执行过程](https://reactive.how/rxjs/)

RxJS 是一个库，它通过使用 observable 序列来编写异步和基于事件的程序。
它提供了一个核心类型 Observable，通过操作符可以把异步事件作为集合来处理。

# 核心概念

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
| - | - | - |
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
let stream$ = new Observable(function(observer) {
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

## Observer 观察者

> 观察者是由 Observable 发送的值的消费者

观察者只是一组回调函数的集合，每个回调函数对应一种 Observable 发送的通知类型：next、error 和 complete 
```js
import {Observable} from "rxjs";

let stream$ = new Observable();

let observer = {
  next: x => console.log('Observer got a next value: ' + x),
  error: err => console.error('Observer got an error: ' + err),
  complete: () => console.log('Observer got a complete notification'),
};

stream$.subscribe(observer);
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

内置调度器一般分为4种

| 调度器 | 目的 |
| null | 不传递任何调度器的话，会以同步递归的方式发送通知。用于定时操作或尾递归操作。|
| Rx.Scheduler.queue | 当前事件帧中的队列调度(蹦床调度器)。用于迭代操作。|
| Rx.Scheduler.asap | 微任务的队列调度，它使用可用的最快速的传输机制，比如 Node.js 的 process.nextTick() 或 Web Worker 的 MessageChannel 或 setTimeout 或其他。用于异步转换。|
| Rx.Scheduler.async | 使用 setInterval 的调度。用于基于时间的操作符。|

# 其他语言实现

* [RxJava](https://github.com/ReactiveX/RxJava)
* [RxGo](https://github.com/ReactiveX/RxGo)
* [RxPython](https://github.com/ReactiveX/RxPY)
* [RxPHP](https://github.com/ReactiveX/RxPHP)
* [RxSwift](https://github.com/ReactiveX/RxSwift)
* [RxAndroid](https://github.com/ReactiveX/RxAndroid)
* [RxDart](https://github.com/ReactiveX/rxdart)
* [RxCpp](https://github.com/ReactiveX/RxCpp)
