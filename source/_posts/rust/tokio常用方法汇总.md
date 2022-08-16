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

# tokio::task

tokio::task模块本身提供了几个函数：

* spawn：向runtime中添加新异步任务
* spawn_blocking：生成一个blocking thread并执行指定的任务
* block_in_place：在某个worker thread中执行同步任务，但是会将同线程中的其它异步任务转移走，使得异步任务不会被同步任务饥饿
* yield_now: 立即放弃CPU，将线程交还给调度器，自己则进入就绪队列等待下一轮的调度
* unconstrained: 将指定的异步任务声明未不受限的异步任务，它将不受tokio的协作式调度，它将一直霸占当前线程直到任务完成，不会受到tokio调度器的管理
* spawn_local: 生成一个在当前线程内运行，一定不会被偷到其它线程中运行的异步任务

# tokio::task::spawn()

直接在当前的runtime中生成一个异步任务。
```rust
use chrono::Local;
use std::thread;
use tokio::{self, task, runtime::Runtime, time};

fn now() -> String {
    Local::now().format("%F %T").to_string()
}

fn main() {
    let rt = Runtime::new().unwrap();
    let _guard = rt.enter();
    task::spawn(async {
        time::sleep(time::Duration::from_secs(3)).await;
        println!("task over: {}", now());
    });

    thread::sleep(time::Duration::from_secs(4));
}

```

# tokio::task::spawn_blocking

生成一个 blocking thread 来执行指定的任务。不阻塞当然任务
```rust
#![allow(unused)]
fn main() {
let join = task::spawn_blocking(|| {
    // do some compute-heavy work or call synchronous code
    "blocking completed"
});

let result = join.await?;
    assert_eq!(result, "blocking completed");
}
```

# tokio::task::block_in_place
block_in_place()的目的和spawn_blocking()类似。

区别在于spawn_blocking()会新生成一个blocking thread来执行指定的任务，而block_in_place()是在当前worker thread中执行指定的可能会长时间运行或长时间阻塞线程的任务，但是它会先将该worker thread中已经存在的异步任务转移到其它worker thread，使得这些异步任务不会被饥饿。

# tokio::task::yield_now
让当前任务立即放弃CPU，worker thread交还给调度器，任务自身则进入调度器的就绪队列等待下次被轮询调度。类似于其它异步系统中的next_tick行为。

# tokio::task::unconstrained 

task::unconstrained() 创建一个不受限制不受调度器管理的异步任务，它将不会参与调度器的协作式调度，可以认为是将这个异步任务暂时脱离了调度管理。
这样一来，即便该任务中遇到了本该阻塞而放弃线程的操作，也不会去放弃，而是直接阻塞该线程。

因此，unconstrained()创建的异步任务将会使得同线程的其它异步任务被饥饿

# 取消任务

正在执行的异步任务可以随时被abort()取消，取消之后的任务返回JoinError类型。

```rust
use tokio::{self, runtime::Runtime, time};

fn main() {
    let rt = Runtime::new().unwrap();

    rt.block_on(async {
        let task = tokio::task::spawn(async {
            time::sleep(time::Duration::from_secs(10)).await;
        });

        // 让上面的异步任务跑起来
        time::sleep(time::Duration::from_millis(1)).await;
        task.abort();  // 取消任务
        // 取消任务之后，可以取得JoinError
        let abort_err: JoinError = task.await.unwrap_err();
        println!("{}", abort_err.is_cancelled());
    })
}

```

# tokio::task::LocalSet
将异步任务固定在本线程中，不能移动到其他线程

可以使用LocalSet的spawn_local()向该队列中添加异步任务。但是，添加的异步任务不会直接执行，只有对LocalSet调用await或调用LocalSet::run_until()或LocalSet::block_on()的时候，才会开始运行本地队列中的异步任务。调用后两个方法会进入LocalSet的上下文环境。

```rust
use chrono::Local;
use tokio::{self, runtime::Runtime, time};

fn now() -> String {
    Local::now().format("%F %T").to_string()
}

fn main() {
    let rt = Runtime::new().unwrap();
    let local_tasks = tokio::task::LocalSet::new();

    // 向本地任务队列中添加新的异步任务，但现在不会执行
    local_tasks.spawn_local(async {
        println!("local task1");
        time::sleep(time::Duration::from_secs(5)).await;
        println!("local task1 done");
    });

    local_tasks.spawn_local(async {
        println!("local task2");
        time::sleep(time::Duration::from_secs(5)).await;
        println!("local task2 done");
    });

    println!("before local tasks running: {}", now());
    rt.block_on(async {
        // 开始执行本地任务队列中的所有异步任务，并等待它们全部完成
        local_tasks.await;
    });
}
```

除了LocalSet::spawn_local()可以生成新的本地异步任务，tokio::task::spawn_local()也可以生成新的本地异步任务，但是它的使用有个限制，必须在LocalSet上下文内部才能调用。

```rust
use chrono::Local;
use tokio::{self, runtime::Runtime, time};

fn now() -> String {
    Local::now().format("%F %T").to_string()
}

fn main() {
    let rt = Runtime::new().unwrap();
    let local_tasks = tokio::task::LocalSet::new();

    // LocalSet::block_on进入LocalSet上下文
    local_tasks.block_on(&rt, async {
        tokio::task::spawn_local(async {
            time::sleep(time::Duration::from_secs(4)).await;
        }).await.unwrap();
    });
    println!("all local tasks done: {}", now());
}

```

调用LocalSet::block_on()和LocalSet::run_until()时均需指定一个异步任务(Future)作为其参数，它们都会立即开始执行该异步任务以及本地任务队列中已存在的任务，
但是这两个函数均只等待其参数对应的异步任务执行完成就返回。
<details>
  <summary>点击时的区域标题</summary>
    ```rust
    use std::thread;
    use chrono::Local;
    use tokio::{self, runtime::Runtime, time};
    
    fn now() -> String {
    Local::now().format("%F %T").to_string()
    }
    
    fn main() {
    let rt = Runtime::new().unwrap();
    let local_tasks = tokio::task::LocalSet::new();
    
        local_tasks.spawn_local(async {
            println!("local task1");
            time::sleep(time::Duration::from_secs(2)).await;
            println!("local task1 done {}", now());
        });
    
        // task2要睡眠10秒，它将被第一次local_tasks.block_on在3秒后中断
        local_tasks.spawn_local(async {
            println!("local task2");
            time::sleep(time::Duration::from_secs(10)).await;
            println!("local task2 done, {}", now());
        });
    
        println!("before local tasks running: {}", now());
        local_tasks.block_on(&rt, async {
            tokio::task::spawn_local(async {
                println!("local task3");
                time::sleep(time::Duration::from_secs(3)).await;
                println!("local task3 done: {}", now());
            }).await.unwrap();
        });
    
        // 线程阻塞15秒，此时task2睡眠10秒的时间已经过去了，
        // 当再次进入LocalSet时，task2将可以直接被唤醒
        thread::sleep(std::time::Duration::from_secs(15));
    
        // 再次进入LocalSet
        local_tasks.block_on(&rt, async {
            // 先执行该任务，当遇到睡眠1秒的任务时，将出现任务切换，
            // 此时，调度器将调度task2，而此时task2已经睡眠完成
            println!("re enter localset context: {}", now());
            time::sleep(time::Duration::from_secs(1)).await;
            println!("re enter localset context done: {}", now());
        });
        println!("all local tasks done: {}", now());
    }
    
    ```
</details>

使用run_until()两次进入LocalSet上下文的示例，和block_on()类似，区别仅在于它只能在Runtime::block_on()内或[tokio::main]注解的main函数内部被调用。

<details>
  <summary>展开代码：</summary>
    ```rust
    use chrono::Local;
    use tokio::{self, runtime::Runtime, time};
    
    fn now() -> String {
    Local::now().format("%F %T").to_string()
    }
    
    fn main() {
    let rt = Runtime::new().unwrap();
    let local_tasks = tokio::task::LocalSet::new();
    
        local_tasks.spawn_local(async {
            println!("local task1");
            time::sleep(time::Duration::from_secs(5)).await;
            println!("local task1 done {}", now());
        });
    
        println!("before local tasks running: {}", now());
        rt.block_on(async {
            local_tasks
                .run_until(async {
                    println!("local task2");
                    time::sleep(time::Duration::from_secs(3)).await;
                    println!("local task2 done: {}", now());
                })
                .await;
        });
    
        thread::sleep(std::time::Duration::from_secs(10));
        rt.block_on(async {
            local_tasks
                .run_until(async {
                    println!("local task3");
                    tokio::task::yield_now().await;
                    println!("local task3 done: {}", now());
                })
                .await;
        });
        println!("all local tasks done: {}", now());
    }
    ```
</details>


block_on() 需要指定一个runtime作为其参数  local_tasks.block_on(&rt, async {})
run_until() 只能在Runtime::block_on()内或[tokio::main]注解的main函数内部被调用
