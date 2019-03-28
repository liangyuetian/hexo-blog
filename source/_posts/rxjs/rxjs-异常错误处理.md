---
title: rxjs-异常错误处理
playlist:
  - name: 近我者甜呀
    artist: 黑猫
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: //p2.music.126.net/Q0T1QKMyaBKiWOMNI_E9Nw==/109951163246464748.jpg?param=90y90
date: 2019-01-08 21:38:01
tags: 
    - rxjs
    - 操作符
categories: RxJs
---

rxjs 异常错误处理
catch
retry retryWhen 重试
finally

<!-- more -->
### catch 捕获上游产生的异常错误
```js
const throwOnUnluckyNumber = value => {
  if (value === 4) {
      throw new Error('unlucky number 4')
  }
  return value
}
const source$ = Observable.range(1, 5)
const error$ = source$.map(throwOnUnluckyNumber)
const catch$ = error$.catch((err, caught$) => Observable.of(8))
// catch 接受两个参数，
// 第一个参数是error,也就是被捕获的错误
// 第二个参数是一个Observabled对象，代表上游紧邻的那个Observable对象，返回的数据会被当恢复现场用的数据传递给下游
```

### retry 
catch 主要用于恢复，但是这种恢复只是往数据流管道里塞另外的数据
让数据流得以继续，很多时候，这样还是不够的，毕竟塞进去的数据并不是真正预期的数据，
这时候，如果重来一次有肯呢个获得正确结果，就应该用上‘重试’试一下
```js
const throwOnUnluckyNumber = value => {
  if (value === 4) {
      throw new Error('unlucky number 4')
  }
  return value
}
const source$ = Observable.range(1, 5)
const error$ = source$.map(throwOnUnluckyNumber)
const retry$ = error$.retry(2); // 重试2次
const catch$ = retry$.catch((err, caught$) => Observable.of(8))
// retry 接受一个参数数值参数number,用于指定重试的次数，
// 如果number 负数或没有number参数，那么就是无限次retry
```

### retryWhen 延迟重试,有节奏的控制retry
retryWhen 接受一个函数参数，这个参数称为 notifer ,用于控制重试的节奏和次数
notifer有一个参数名为error$(由错误组成的Observable对象)
notifer每吐出一个数据，那么retry就会重试一次

#### 延时重试
```js
const source$ = Observable.range(1, 5)
const error$ = source$.map(throwOnUnluckyNumber)
const retry$ = error$.retryWhten(err$ => err$.delay(1000)); // 1000毫秒后延迟开始重试
```

#### 用retryWhen 实现 retry 功能
```js
Observable.prototype.retryWhen = function (maxCount) {
  return this.retryWhen( err$ => {
    return err$.scan((errorCount, err) => {
      if (errorCount >= maxCount) {
        throw err;
      }
      return errprCount + 1;
    }, 0)
  })
}
```

#### 延迟并有上限的重试
```js
Observable.prototype.retryWhen = function (maxCount, delayMilliseconds) {
  return this.retryWhen( err$ => {
    return err$.scan((errorCount, err) => {
      if (errorCount >= maxCount) {
        throw err;
      }
      return errprCount + 1;
    }, 0).delay(delayMilliseconds)
  })
}
```

#### 递增延时重置
重试策略每次重试之间的延时间隔不是固定的，这样更加科学
```js
Observable.prototype.retryWhen = function (maxCount, delayMilliseconds) {
  return this.retryWhen( err$ => {
    return err$.scan((errorCount, err) => {
      if (errorCount >= maxCount) {
        throw err;
      }
      return errprCount + 1;
    }, 0).delayWhen( errorCount => {
      const delayTime = Math.pow(2, errorCount - 1) * initialDelay
      return Observable.timer(delayTime)
    })
  })
}
```

#### finally 用于执行无论出错还是不出错都要做的事情
```js
const source$ = Observable.range(1, 5)
const error$ = source$.map(throwOnUnluckyNumber)
const final$ = error$.retry(2)
  .catch((err, caught$) => Observable.of(8))
  .finally( x => console.log('finally'))
```

## 重试的本质

#### 无论是retryWhen 还是retry ，所谓的重试，其实就是重新订阅一遍上游Observable对象的过程，
#### 在订阅上游的同时，会退订上一次的订阅，所以，如果上游数据吐出数据较快，错误较多，那么也不要紧，只有最后一次的错误才会被订阅！
### 即：重试就是 退订+订阅