---
title: rust日志库：tracing
categories: Rust
abbrlink: 3365806354
date: 2022-08-17 22:48:48
tags:
---

tracing是一个用于检测 Rust 程序以收集结构化的、基于事件的诊断信息的框架。tracing由 Tokio 项目维护，但不需要使用tokio运行时。
[https://course.rs/logs/tracing.html](https://course.rs/logs/tracing.html)
<!-- more -->

严格来说，tracing 并不是一个日志库，而是一个分布式跟踪的 SDK，用于采集监控数据的。

tracing 使用 event 发出日志，使用 subscriber 收集日志

分布式追踪的核心就是在请求的开始生成一个 trace_id，然后将该 trace_id 一直往后透穿，请求经过的每个服务都会使用该 trace_id 记录相关信息，最终将整个请求形成一个完整的链路予以记录下来。

```rust
use tracing::instrument;
use tracing_error::{ErrorLayer, SpanTrace};
use tracing_subscriber::{fmt, registry::Registry};
use log;
use tracing_subscriber::layer::SubscriberExt;
use tracing_subscriber::util::SubscriberInitExt;

fn main() {
    tracing_subscriber::registry()
        .with(fmt::layer())
        .init();

    log::info!("hello word");
    let foo = 32;
    tracing::info!(foo, "hello from tracing");
}

```

在异步编程中使用传统的日志也是存在一些问题的，最大的挑战就在于异步任务的执行没有确定的顺序，那么输出的日志也将没有确定的顺序并混在一起，无法按照我们想要的逻辑顺序串联起来。

归根到底，在于日志只能针对某个时间点进行记录，缺乏上下文信息，而线程间的执行顺序又是不确定的，因此日志就有些无能为力。而 tracing 为了解决这个问题，引入了 <code>span</code> 的概念( 这个概念也来自于分布式追踪 )，一个 <code>span</code> 代表了一个时间段，拥有开始和结束时间，在此期间的所有类型数据、结构化数据、文本数据都可以记录其中。

# 核心概念

## Span

span 最大的意义就在于它可以记录一个过程，也就是在某一段时间内发生的事件流。既然是记录时间段，那自然有开始和结束:

```rust
use tracing::{span, Level};
fn main() {
    let span = span!(Level::TRACE, "my_span");

    // `enter` 返回一个 RAII ，当其被 drop 时，将自动结束该 span
    let enter = span.enter();
    // 这里开始进入 `my_span` 的上下文
    // 下面执行一些任务，并记录一些信息到 `my_span` 中
    // ...
} // 这里 enter 将被 drop，`my_span` 也随之结束
```
## Event 事件
Event 代表了某个时间点发生的事件，这方面它跟日志类似，但是不同的是，Event 还可以产生在 span 的上下文中。

```rust
use tracing::{event, span, Level};
use tracing_subscriber::{fmt, layer::SubscriberExt, util::SubscriberInitExt};

fn main() {
    tracing_subscriber::registry().with(fmt::layer()).init();
    // 在 span 的上下文之外记录一次 event 事件
    event!(Level::INFO, "something happened");

    let span = span!(Level::INFO, "my_span");
    let _guard = span.enter();

    // 在 "my_span" 的上下文中记录一次 event
    event!(Level::DEBUG, "something happened inside my_span");
    // 输出 在这里自动捕获了 环境变量
    // 2022-08-17T15:54:52.392970Z DEBUG my_span: hello_tracing: something happened inside my_span
}
```

## Collect收集器 
当 Span 或 Event 发生时，它们会被实现了 Collect 特征的收集器所记录或聚合。这个过程是通过通知的方式实现的：当 Event 发生或者 Span 开始/结束时，会调用 Collect 特征的相应方法通知 Collector。


# span! 宏

1.生成一个span上下文
<code>let span = span!(Level::TRACE, "my_span");</code>

2.进入 span 上下文
let enter = span.enter();

3.销毁或者推出上下文
    a. 在作用域结束时自动结束该span
    b. 使用 drop(span)

可以嵌套！

# #[instrument]

如果想要将某个函数的整个函数体都设置为 span 的范围，最简单的方法就是为函数标记上 #[instrument]，此时 tracing 会自动为函数创建一个 span，span 名跟函数名相同，在输出的信息中还会自动带上函数参数。

自动打日志：
```rust
use tracing::{info, instrument};
use tracing_subscriber::{fmt, layer::SubscriberExt, util::SubscriberInitExt};

#[instrument]
fn foo(ans: i32) {
    info!("in foo");
}

fn main() {
    tracing_subscriber::registry().with(fmt::layer()).init();
    foo(42);
}
```

# in_scope

对于没有内置 tracing 支持或者无法使用 #instrument 的函数，例如外部库的函数，我们可以使用 Span 结构体的 in_scope 方法，它可以将同步代码包裹在一个 span 中：

```rust
#![allow(unused)]
fn main() {
use tracing::info_span;

let json = info_span!("json.parse").in_scope(|| serde_json::from_slice(&buf))?;
}
```

# 格式化

可以直接输出结构体字符串，使用 fmt::Debug
```rust
#![allow(unused)]
fn main() {
 #[derive(Debug)]
struct MyStruct {
    field: &'static str,
}

let my_struct = MyStruct {
    field: "Hello world!",
};

// `my_struct` 将使用 Debug 的形式输出
event!(Level::TRACE, greeting = ?my_struct);
// 等价于:
event!(Level::TRACE, greeting = tracing::field::debug(&my_struct));

// 下面代码将报错, my_struct 没有实现 Display
// event!(Level::TRACE, greeting = my_struct);

// 日志输出 -> TRACE test_tracing: greeting=MyStruct { field: "Hello world!" }
}
```

? 符号用于说明该字段将使用 fmt::Debug 来格式化。
% 说明字段将用 fmt::Display 来格式化。

```rust
#![allow(unused)]
fn main() {
// `my_struct.field` 将使用 `fmt::Display` 的格式化形式输出
event!(Level::TRACE, greeting = %my_struct.field);
// 等价于:
event!(Level::TRACE, greeting = tracing::field::display(&my_struct.field));

// 作为对比，大家可以看下 Debug 和正常的字段输出长什么样
event!(Level::TRACE, greeting = ?my_struct.field);
event!(Level::TRACE, greeting = my_struct.field);

// 下面代码将报错, my_struct 没有实现 Display
// event!(Level::TRACE, greeting = %my_struct);
}
```
输出
```shell
2022-08-17T16:10:29.725418Z TRACE my_span: hello_tracing: greeting=Hello world!
2022-08-17T16:10:29.725432Z TRACE my_span: hello_tracing: greeting=Hello world!
2022-08-17T16:10:29.725443Z TRACE my_span: hello_tracing: greeting="Hello world!"
2022-08-17T16:10:29.725466Z TRACE my_span: hello_tracing: greeting="Hello world!"
```

# 文件输出

[https://github.com/tokio-rs/tracing/tree/master/tracing-appender](https://github.com/tokio-rs/tracing/tree/master/tracing-appender)
[https://docs.rs/tracing-appender/latest/tracing_appender/non_blocking/index.html](https://docs.rs/tracing-appender/latest/tracing_appender/non_blocking/index.html)
使用 <code>tracing-appender</code> 

<pre>
<code>
let file_appender = tracing_appender::rolling::hourly("./log", "prefix.log");
let (non_blocking, _guard) = tracing_appender::non_blocking(file_appender);
tracing_subscriber::fmt()
    .with_writer(non_blocking)
    .init();
</code>
</pre>