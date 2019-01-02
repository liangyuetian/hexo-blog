---
title: 合并数据操作符
date: 2019-01-02 23:01:37
tags: rxjs
---

合并数据操作符
concat，concatAll，merge，mergeAll，zip，zipAll
combineLatest，combineAll，withLatestFrom
race，startWith，forkJoin，switch，exhaust

<!-- more -->

## 第二大类：合并数据操作符

| 操作符 | 功能 |
| ------ | ------ |
| concat concatAll | 把多个数据以首尾相连方式合并 |
| merge mergeAll | 把多个数据流中数据以先到先得方式合并 |
| zip zipAll | 把多个数据流中最新产生的数据以一一对应方式合并 |
| combineLatest combineAll withLatestFrom | 持续合并多个数据流中最新产生的数据 |
| race | 从多个数据流中选取第一个产生内容的数据流 |
| startWith | 在数据流前面添加一个指定数据 |
| forkJoin | 只获取多个数据流最后产生的那个数据 |
| switch exhaust | 从高阶数据流中切换数据源 |

例子：
#### concat 首尾相连
```js
// concat既有实例操作符，也有静态操作符方式
const source1$ = Observable.of(1, 2, 3);
const source2$ = Observable.of(4, 5, 6);
const concated$ = source1$.concat(source2$);
// 还可以
const concated$ = Observable.concat(source1$, source2$);
/** 
concat 的工作方式
1 从第一个Observable 对象获取数据，把数据传给下游
2 当第一个Observable 对象 complete 后，concat就会去第二个Observable对象获取数据
3 依次类推 直到最后一个Observable完结之后，concat产生的Observable也就完结了
注意：
concat 会在第一个Observable对象完结后去订阅下一个Observable对象，所以参与到conct的Observable对象应该都能完结。如果其中有一个Observable对象不会完结，那么后面的Observable对象永远都没有上场的机会
*/
```

#### merge 先到先得快速通过
```js
/**
merge 与concat不同，merge会第一事件订阅所有的上游Observable,然后对上游的数据采取‘先到先得’的策略，任何一个Observale只要有数据推下来，就立刻转给下游Observable对象。
merge 在第一时间就订阅上游的所有Observable对象，所以某个上游数据永不完结，也不影响其他的Observable对象
*/
const source1$ = Observable.timer(0, 1000).map(x => x+"A");
const source2$ = Observable.timer(500, 1000).map(x => x+"B");
const merged$ = Observable.merge(source1$, source2$);
merged$.subscribe(
    console.log,
    null,
    () => console.log('complete')
)
// 以上代码会每隔500毫秒输出一行结果，永不停歇
// 0A 0B 1A 1B 2A 2B ...

// 同步限流
// merge 可以有一个可选参数concurent,用于指定可以同时合并的Observable对象的个数
const source$1 = Observable.timer(0, 1000).map(x => x+"A");
const source$2 = Observable.timer(0, 1000).map(x => x+"B");
const source$3 = Observable.timer(0, 1000).map(x => x+"C");
const merged$ = source$1.merge(source$2, source$3);
```

#### zip:拉链式组合
```js
// zip 要像拉链一样做到一对一咬合
// 只要任何一个上游的Observable对象完结，zip只要给这个完结的Observable对象吐出所有数据找到配对的数据，那么zip就会给下游一个complete信号
const source$1 = Observable.interval(1000);
const source$2 = Observable.of('a', 'b', 'c');
const ziped$ = Observable.zip(source$1, source$2);
// 输出：
[0, 'a']
[1, 'b']
[2, 'c']
// complete

// 数据积压问题
// 可以使用节流(throttle)，防抖(debounce) 相关系列的操作符来抛弃不需要的值
```

#### combineLatest 合并最后一个数据
```js
// 当任何一个上游Observable产生数时，从所有输入Observable对象中拿最后一次产生的数据（最新数据）,然后把这些数据组合起来传给下游。注意，这种方式和zip不一样，zip对上游数据只使用一次，用过一个数据之后就不会再用，但是combineLatest可能会反复使用上游产生的最新数据，只要上游不产生新的数据，那combineLatest就会反复使用这个上游最后一次产生的数据
// 只要当所有上游Observable都完结后，combineLatest才会给下游一个complete信号，表示不会有任何数据更新了

// 定制下游数据
// combinLatest的最后一个参数可以是一个函数，这里我们称为project,project的作用是让combineLatest把所有上游数据的最新数据扔给下游之前做一下组个处理，这样就可以不用传递一个数组下去，可以传递任何由最新数据产生的对象
const source$1 = Observable.interval(1000)
const source$2= Observable.timer(0, 1000)
const project = (a, b) => `${a} and ${b}`
const result$ source1$.combineLatest(source2$, project)

// 多重依赖问题
const origin$ = Observable.timer(0, 1000)
const source$1 = origin$.map(x => x+"A")
const source$1 = origin$.map(x => x+"B")
const result$ = source1$.combineLatest(source$2);
// 这里source$1和source$2会被origin$同时触发， 同一时间内应该只得到1个输出才对，但是现实是得到2个
// 1秒后
['1a', '0b']
['1a', '1b']
// 2秒后
['2a', '1b']
['2a', '2b']
```

#### withLatestFrom 类似于 combineLatest,但是上游只能由一个Observable对象驱动
```js
// withLatestFrom只有实例操作符的形式(因为：使用此操作符时，Observable对象地位不对等了)
const source$1 = Observable.interval(2000)
const source$2= Observable.timer(0, 1000)
const result$ = source$1.withLatestFrom(source$2, (a, b) => a+b);
// 作为参数的Observable对象只能贡献数据，不能控制产生数据的时机，节奏
// 每隔2秒输出一行
// 1
// 4
// 7
// 10

// withLatestFrom combineLatest 的选择：
// 如果要合并完全独立的Observable对象，使用combineLatest
// 如果要把一个Observable对象‘映射’新的数据流，同时要从其他Observable对象获取最新数据，那么使用withLatestFrom
```

#### race 胜者通吃
```js
// rece就是竞争，多个Observable对象在一起，看谁先产生数据，不过这种竞争是十分残酷的，胜者通吃，败者则市区所有机会
const source$1 = Observable.interval(2000)
const source$2 = Observable.timer(0, 1000)
const result$ = source$1.race(source$2);
// 如果race确定了胜利者，那么就会退订其他输入的Observable对象
```

#### startWith
```js
// startWith只要实例操作符的形式，其功能是让一个Observable对象在被订阅的时候总是先吐出指定的若干数据，
const source$1 = Observable.timer(0, 1000)
const result$ = source$1.startWith('start'); // 支持多个参数，同步输出
// 输出
// start 立刻输出
// 0 1秒后
// 1 2秒后
```

#### forkJoin ~== Promise.all
```js
// forkJoin只要静态操作符，接受多个Observable对象作为参数，它只会产生一个数据，因为他会等待所有参数Observable对象对象的最后一个数据，
// 也就是说，只要当所有Observable都完结，forkJoin会把所有输入Observable对象产生的最后一个数据合并成下游唯一的数据
// 所以说forkJoin就是rxjs界的Promise.all
const source$1 = Observable.interval(2000)
const source$2 = Observable.timer(0, 1000)
const result$ = source$1.forkJoin(source$2);
```

#### 高阶Observable 高阶数据流
```js
// 高阶函数就是产生函数的函数：参数为函数，返回也是函数
// 简单的高阶Observable
const hot$ = Observable.interval(1000).take(2).map(x => Observable.interval(1500).map(y => x+':'+y).take(2));
// 注意：高阶Observable完结，不代表内部Observable完结，但是内部Observable却不会岁随主干Observable的完结而完结，因为作为独立Observable，他们有自己的生命周期

// 高阶Observable的意义
// 高阶Observable的本质是用管理数据的方式来管理多个Observable对象，它的存在意义就在与此
```

#### 操作高阶Observable的合并类操作符

* concatAll
* merageAll
* zipAll
* combineAll(例外)

all代表全部，这些操作符功能有差异，但都是把一个高阶Observable的所有的内部Observable组合起来，所有这类操作符全部都只有实力操作符的形式

#### concatAll 
```js
// concatAll 只有一个上游Observable对象，这个observable对象预期是一个高阶Observable对象，concatAll会对内部的Observable对象做concat操作
const ho$ = Observable.interval(1000)
            .take(2)
            .map(x => Observable.interval(1500).map(y => x+':'+y).take(2));
const concated$ = ho$.concatAll();
// 0:0 # 开始订阅第一个内部Observable
// 0:1 
// 1:0 # 开始订阅第二个内部Observable
// 1:1
// concatAll 首先会订阅上游产生的第一个内部Observable对象，抽取其中的数据，然后只有当第一个Observable对象完结的时候，才会去订阅第二个Observable对象。
// 也就是说，虽然高阶Observable对象已经产生了第二个Observable对象，不代表concatAll会立刻去订阅它，因为和这个Observable对象是懒执行，所以不去订阅自然也不会产生数据。
```

#### mergeAll
```js
// mergeAll 就是处理高阶Observable的merge,
const ho$ = Observable.interval(1000)
            .take(2)
            .map(x => Observable.interval(1500).map(y => x+':'+y).take(2));
const concated$ = ho$.margeAll();
// mergeAll 对内部的Observable的订阅策略和concatAll不同，mergeAll只要发现上游产生一个内部Observable就会立刻订阅，并从中抽取数据
```

#### zipAll
```js
const ho$ = Observable.interval(1000)
            .take(2)
            .map(x => Observable.interval(1500).map(y => x+':'+y).take(2));
const concated$ = ho$.zipAll();
// ["0:0", "1:0"]
// ["0:1", "1:1"]

const ho$ = Observable.interval(1000).take(2).concat(Observable.never()) // 形成了只有两个数据的永不完结的数据流
            .map(x => Observable.interval(1500).map(y => x+':'+y).take(2));
const concated$ = ho$.zipAll();
// 现在zipAll的上游是一个永不完结的Observable，当它拿到2个内部Observable的时候，无法确定是不是还有新的内部Observable产生，而根据拉链的工作方式，来自不同数据源的数据要一对一配对，
// 这样一来，zipAll就只能等待，等待上游高阶Observable完结，这样才能确定内部Observable对象的数量，如果上游的高阶Observable不完结，那么zipAll 就不会开始工作
```

#### switch 切换输入Observable
```js
// switch 的含义就是 切换，总是切换到最新的内部Observable对象获取数据，每当switch的上游高阶Observable产生一个内部Observable对象，switch都会立刻订阅最新的内部Observable对象上
// 如果已经订阅了之前的内部Observable对象，就会退订那个多事的内部Observable对象，这个 用上新的，舍弃旧的 动作，就是切换
const ho$ = Observable.interval(1000)
            .take(2)
            .map(x => Observable.interval(1500).map(y => x+':'+y).take(2));
const result$ = ho$.switch();
// 1:0
// 1:1
// switch 首先订阅了第一个内部Observable对象，但是这个内部对象还没来得及产生第一个数据0:0(1000ms + 1500ms 后)，第二个内部Observable对象就产生了(100ms + 1000ms 后)，
// 这样switch就会切换动作，切换到第二个内部Observable上，因为之后没有新的内部Observable对象产生了，switch就会一直从第二个内部Observable对象获取数据，于是最后得到的数据就是 1:0,1:1

// 在上游Observable产生新的内部Observable时进行切换。订阅的是内部的Observable

// 注意和 race 不同，race 是别人没有机会，而switch可以翻来覆去一直抢

// 完结条件：1 上游高阶Observable已经完结; 2 当前内部Observable已经完结
```

#### exhaust 耗尽
```js
// 在耗尽当前内部Observable的数据之前不会切换到下一个内部Observable对象
const ho$ = Observable.interval(1000)
            .take(3)
            .map(x => Observable.interval(700).map(y => x+':'+y).take(2));
const result$ = ho$.exhaust();
// exhaust 首先从第一个内部Observable对象获取数据，然后再考虑后续的内部Observable对象
// 第二个内部Observable生不逢时，当它产生的时候第一个内部Observable对象还没有完结，这时候exhaust会直接忽略第二个Observable对象，甚至不会去订阅它，、
// 第三个内部Observable对象会被订阅病提取数据，是因为在它出现之前，第一个内部Observable都已经完结了

// 完结条件：1 上游Observable对象完结; 2 最新的内部Observable对象完结
```