---
title: tokio常用方法汇总
categories: Rust
abbrlink: 61015457
date: 2022-08-15 23:24:02
tags:
---

tokio常用方法汇总

<!-- more -->

大佬博客
[https://rust-book.junmajinlong.com/ch100/01_understand_tokio_runtime.html](https://rust-book.junmajinlong.com/ch100/01_understand_tokio_runtime.html)

# tokio::spawn

创建一个异步任务,不仅可以单独的走异步任务，异步任务还可以返回结果，使用 await 接收
```rust
use tokio;

#[tokio::main]
async fn main() {
    tokio::spawn(async {
        println("OX")
    });

    let handle = tokio::spawn(async {
        10086
    });

    let out = handle.await.unwrap();
    println!("GOT {}", out);

    async fn more_async_work() -> &'static str {
        tokio::spawn(async {
            "two"
        }).await.unwrap_or("error")
    }

    async fn do_stuff_async() -> &'static str {
        tokio::spawn(async {
            "two"
        }).await.unwrap_or("error")
    }

    println("{}", do_stuff_async().await)
}
```

# tokio::join!

使用 tokio::join! 宏同时执行多个异步任务
```rust
async fn do_stuff_async() -> &'static str {
    let s = tokio::spawn(async {
        "one"
    }).await.unwrap();
    s
}

async fn more_async_work() -> &'static str {
    tokio::spawn(async {
        "two"
    }).await.unwrap_or("error")
}

#[tokio::main]
async fn main() {
    let (first, second) = tokio::join!(
        do_stuff_async(),
        more_async_work()
    );

    println!("{}, {}, {}", first, second, more_async_work().await)
    // one, two, two

}
```

# tokio::select!

select! 允许同时等待多个计算操作，然后当其中一个操作完成时就退出等待
注意，select 是在单线程中执行

目前来说，select! 最多可以支持 64 个分支，每个分支形式如下：

<模式> = <async 表达式> => <结果处理>,
```rust
use tokio::sync::oneshot;

async fn some_operation() -> String {
    // 在这里执行一些操作...
}

#[tokio::main]
async fn main() {
    let (mut tx1, rx1) = oneshot::channel();
    let (tx2, rx2) = oneshot::channel();

    tokio::spawn(async {
        // 等待 `some_operation` 的完成
        // 或者处理 `oneshot` 的关闭通知
        tokio::select! {
            val = some_operation() => {
                let _ = tx1.send(val);
            }
            _ = tx1.closed() => {
                // 收到了发送端发来的关闭信号
                // `select` 即将结束，此时，正在进行的 `some_operation()` 任务会被取消，任务自动完成，
                // tx1 被释放
            }
        }
    });

    tokio::spawn(async {
        let _ = tx2.send("two");
    });

    tokio::select! {
        val = rx1 => {
            println!("rx1 completed first with {:?}", val);
        }
        val = rx2 => {
            println!("rx2 completed first with {:?}", val);
        }
    }
}
```
select! 还能返回一个值:
```rust
async fn computation1() -> String {
    // .. 计算
}

async fn computation2() -> String {
    // .. 计算
}

#[tokio::main]
async fn main() {
    let out = tokio::select! {
        res1 = computation1() => res1,
        res2 = computation2() => res2,
    };

    println!("Got = {}", out);
}
```

# tokio::try_join!

使用 try_join 拦截可能会出错的异步任务

```rust
use tokio::task::JoinHandle;

async fn do_stuff_async() -> Result<(), &'static str> {
 // async work
    Err("failed")
}

async fn more_async_work() -> Result<(), &'static str> {
 // more here
    Ok(())
}

async fn flatten<T>(handle: JoinHandle<Result<T, &'static str>>) -> Result<T, &'static str> {
 match handle.await {
     Ok(Ok(result)) => Ok(result),
     Ok(Err(err)) => Err(err),
     Err(err) => Err("handling failed"),
 }
}

#[tokio::main]
async fn main() {
 let handle1 = tokio::spawn(do_stuff_async());
 let handle2 = tokio::spawn(more_async_work());
 match tokio::try_join!(flatten(handle1), flatten(handle2)) {
     Ok(val) => {
         // do something with the values
     }
     Err(err) => {
         println!("Failed with {}.", err);
         assert_eq!(err, "failed");
     }
 }
}
```



# tokio::runtime
大佬博客
[https://rust-book.junmajinlong.com/ch100/01_understand_tokio_runtime.html](https://rust-book.junmajinlong.com/ch100/01_understand_tokio_runtime.html)

要使用tokio，需要先创建它提供的异步运行时环境(Runtime)，然后在这个Runtime中执行异步任务。

```rust
use tokio;

fn main() {
  // 创建runtime
  let rt = tokio::runtime::Runtime::new().unwrap();
}
```

```rust
use tokio;

fn main() {
  // 创建带有线程池的runtime
  let rt = tokio::runtime::Builder::new_multi_thread()
    .worker_threads(8)  // 8个工作线程
    .enable_io()        // 可在runtime中使用异步IO
    .enable_time()      // 可在runtime中使用异步计时器(timer)
    .build()            // 创建runtime
    .unwrap();
}
```
tokio提供了两种工作模式的runtime：

1.单一线程的runtime(single thread runtime，也称为current thread runtime)
2.多线程(线程池)的runtime(multi thread runtime)

创建单一线程的runtime
```rust
#![allow(unused)]
fn main() {
// 创建单一线程的runtime
let rt = tokio::runtime::Builder::new_current_thread().build().unwrap();
}
```
创建多线程的runtime，查看其线程数：
```rust
use tokio;

fn main(){
    let rt = tokio::runtime::Runtime::new().unwrap();
    std::thread::sleep(std::time::Duration::from_secs(10));
}

```

#[tokio::main]创建的是多线程runtime，还有以下几种方式创建多线程runtime：

#[tokio::main(flavor = "multi_thread"] // 等价于#[tokio::main]
#[tokio::main(flavor = "multi_thread", worker_threads = 10))]
#[tokio::main(worker_threads = 10))]

# 进入runtime

block_on()是进入runtime的主要方式。但还有另一种进入runtime的方式：enter()。

block_on()进入runtime时，会阻塞当前线程，enter()进入runtime时，不会阻塞当前线程，它会返回一个EnterGuard。EnterGuard没有其它作用，它仅仅只是声明从它开始的所有异步任务都将在runtime上下文中执行，直到删除该EnterGuard。

删除EnterGuard并不会删除runtime，只是释放之前的runtime上下文声明。因此，删除EnterGuard之后，可以声明另一个EnterGuard，这可以再次进入runtime的上下文环境。
```rust
use tokio::{Runtime, time};
fn async_task(rt: &Runtime) {
  rt.spawn(async {
    time::sleep(time::Duration::from_secs(10)).await;
  });
}

fn main(){
  let rt = Runtime::new().unwrap();
  rt.block_on(async {
    async_task(&rt);
  });
}
```

```rust
fn main() {
    let rt = Runtime::new().unwrap();

    // 进入runtime，但不阻塞当前线程
    let guard1 = rt.enter();

    // 生成的异步任务将放入当前的runtime上下文中执行
    tokio::spawn(async {
        time::sleep(time::Duration::from_secs(5)).await;
        println!("task1 sleep over: {}", now());
    });
}
```

# 关闭 runtime
```rust
#![allow(unused)]
fn main() {
    let rt = Runtime::new().unwrap();
    drop(rt);
}
```
这里的变量rt，官方手册将其称为runtime的句柄(runtime handle)。

关闭Runtime时，将使得该Runtime中的所有异步任务被移除。完整的关闭过程如下：

1.先移除整个任务队列，保证不再产生也不再调度新任务
2.移除当前正在执行但尚未完成的异步任务，即终止所有的worker thread
3.移除Reactor，禁止接收事件通知

那些已经运行起来的blocking thread以及已经阻塞整个线程的worker thread仍然会执行。

tokio提供了另外两个关闭runtime的方式：shutdown_timeout()和shutdown_background()。
前者会等待指定的时间，如果正在超时时间内还未完成关闭，将强行终止runtime中的所有线程。后者是立即强行关闭runtime。