---
title: Tokio 备忘录
tags:
  - rust
  - io
categories: Rust
abbrlink: 404056903
date: 2022-08-06 21:05:25
---

学习 Tokio 的备忘录，记录一下常用代码
Tokio 专为 IO 密集型应用程序而设计，其中每个单独的任务大部分时间都在等待 IO

内容来源：
[https://tokio.rs/tokio/tutorial](https://tokio.rs/tokio/tutorial)
[https://github.com/rustlang-cn/Rustt/tree/main/Books](https://github.com/rustlang-cn/Rustt/tree/main/Books)

<!-- more -->

# 基本使用

## 加入tokio运行时和创建线程

```rust
use mini_redis::{client, Result};

#[tokio::main]
async fn main() -> Result<()> {
    tokio::spawn(async {
        println!("Hello world");
    });
}
```

[tokio::main] 的展开
```rust
fn main() {
    tokio::runtime::Builder::new_multi_thread()
        .enable_all()
        .build()
        .unwrap()
        .block_on(async {
            println!("Hello world");
        });

    // 或者
    let mut rt = tokio::runtime::Runtime::new().unwrap();
    rt.block_on(async {
        println!("hello");
    })
}
```

## 并发，同时处理多个请求
```rust
use tokio::net::TcpListener;

#[tokio::main]
async fn main() {
    let listener = TcpListener::bind("127.0.0.1:6379").await.unwrap();

    loop {
        let (socket, _) = listener.accept().await.unwrap();
        // A new task is spawned for each inbound socket. The socket is
        // moved to the new task and processed there.
        tokio::spawn(async move {
            process(socket).await;
        });
    }
}
```

还可以有返回值
```rust
#[tokio::main]
async fn main() {
    let handle = tokio::spawn(async {
        // Do some async work
        "return value"
    });

    // Do some other work

    let out = handle.await.unwrap();
    println!("GOT {}", out);
}
```
等待JoinHandle返回一个Result. 当任务在执行过程中遇到错误时，JoinHandle将返回一个Err.
当任务出现紧急情况，或者任务被运行时关闭强制取消时，就会发生这种情况。


## 共享状态

在 Tokio 中有几种不同的方式来共享状态。

1. 使用 Mutex 保护共享状态。
2. 生成一个任务来管理状态并使用消息传递对其进行操作。

### 使用 Mutex 保护共享状态

添加依赖 bytes，请将以下内容添加到您Cargo.toml的 [dependencies]部分中：

bytes = "1"

```rust


use bytes::Bytes;
use std::collections::HashMap;
use std::sync::{Arc, Mutex};
// tokio::sync::Mutex

type Db = Arc<Mutex<HashMap<String, Bytes>>>;


#[tokio::main]
async fn main() {
    let listener = TcpListener::bind("127.0.0.1:6379").await.unwrap();

    println!("Listening");
    // 初始化 HashMap
    let db = Arc::new(Mutex::new(HashMap::new()));

    loop {
        let (socket, _) = listener.accept().await.unwrap();
        // Clone the handle to the hash map.
        let db = db.clone();

        println!("Accepted");
        tokio::spawn(async move {
            process(socket, db).await;
        });
    }
}
```

> Tokio 提供的异步锁只应该在跨多个 .await调用时使用，而且 Tokio 的 Mutex 实际上内部使用的也是 std::sync::Mutex。
> 
> Tokio 提供的锁最大的优点就是：它可以在 .await 执行期间被持有，而且不会有任何问题。但是代价就是，这种异步锁的性能开销会更高。

### 使用 channel 消息传递

Tokio 提供了多种消息通道，可以满足不同场景的需求:

* mpsc, 多生产者，单消费者模式
* oneshot, 单生产者单消费，一次只能发送一条消息
* broadcast，多生产者，多消费者，其中每一条发送的消息都可以被所有接收者收到，因此是广播
* watch，单生产者，多消费者，只保存一条最新的消息，因此接收者只能看到最近的一条消息，例如，这种模式适用于配置文件变化的监听‘

#### 创建消息通道

```rust
use tokio::sync::mpsc;

#[tokio::main]
async fn main() {
    // 创建一个新通道，缓冲队列长度是 32
    let (tx, mut rx) = mpsc::channel(32); // 多生产者，单消费者模式

    // 使用
    let tx2 = tx.clone();
    tx.send("sending from first handle").await;
    tx2.send("sending from second handle").await;
    
    // 消费，只能有一个消费者
    while let Some(message) = rx.recv().await {
        println!("GOT = {}", message);
    }
    // 当所有的发送者都被 Drop 掉后(超出作用域或被 drop(...) 函数主动释放)，就不再会有任何消息发送给该通道，
    // 此时 recv 方法将返回 None，也意味着该通道已经被关闭。
}
```

# IO,文件输入输出
Tokio 中的 I/O 操作和 std 在使用方式上几无区别，最大的区别就是前者是异步的，例如 Tokio 的读写特征分别是 AsyncRead 和 AsyncWrite:

* 有部分类型按照自己的所需实现了它们: TcpStream，File，Stdout
* 还有数据结构也实现了它们：Vec<u8>、&[u8]，这样就可以直接使用这些数据结构作为读写器( reader / writer)

## AsyncRead 和 AsyncWrite

### read 主要有2个方法：
read 和 read_to_end

<code>AsyncReadExt::read</code> 是一个异步方法可以将数据读入缓冲区( buffer )中，然后返回读取的字节数。
<code>AsyncReadExt::read_to_end</code> 方法会从字节流中读取所有的字节，直到遇到 EOF ：

```rust
use tokio::fs::File;
use tokio::io::{self, AsyncReadExt};

#[tokio::main]
async fn main() -> io::Result<()> {
    let mut f = File::open("foo.txt").await?;
    let mut buffer = [0; 10];

    // 由于 buffer 的长度限制，当次的 `read` 调用最多可以从文件中读取 10 个字节的数据
    let n = f.read(&mut buffer[..]).await?;

    // 读取整个文件的内容
    f.read_to_end(&mut buffer).await?;

    println!("The bytes: {:?}", &buffer[..n]);
    Ok(())
}
```

<code>AsyncWriteExt::write</code> 异步方法会尝试将缓冲区的内容写入到写入器( writer )中，同时返回写入的字节数
<code>AsyncWriteExt::write_all</code> 将缓冲区的内容全部写入到写入器中

### write 同样也有2个方法：
write 和 write_all；和 rea差不多

```rust
use tokio::io::{self, AsyncWriteExt};
use tokio::fs::File;

#[tokio::main]
async fn main() -> io::Result<()> {
    let mut file = File::create("foo.txt").await?;

    let n = file.write(b"some bytes").await?;

    // 写入全部
    file.write_all(b"some bytes").await?;
    println!("Wrote the first {} bytes of 'some bytes'.", n);
    Ok(())
}
```

还有其他的一些api
[File ](https://docs.rs/tokio/latest/tokio/fs/struct.File.html)

* File::open
* File::create


[用于操作 File 的 AsyncReadExt 特征](https://docs.rs/tokio/latest/tokio/io/trait.AsyncReadExt.html)
* read 读取多少字节，然后返回
* read_buf 读取字节，并且更新文件指针的游标位置
* read_exact 此函数根据需要读取尽可能多的字节以完全填充指定的缓冲区buf。如果没填完，就报错
* read_u8/read_i8 等，从底层读取器读取一个无符号的 8 位整数。

[用于操作 File 的 AsyncWriteExt 特征](https://docs.rs/tokio/latest/tokio/io/trait.AsyncWriteExt.html)
* write
* write_vectored 
* write_all
* write_all_buf 
* write_u8/write_i8 等

### 其他的一些辅助函数

[https://docs.rs/tokio/1.20.1/tokio/io/fn.copy.html](https://docs.rs/tokio/1.20.1/tokio/io/fn.copy.html)

* copy io::copy(&mut reader, &mut writer).await?;
* copy_bidirectional 互相交换数据
* copy_buf 将读取器的全部内容异步复制到写入器中。
* io::duplex() 双工流； 创建一对新的DuplexStreams，其作用类似于一对连接的套接字。
* io::empty() 创建一个新的空异步读取器。
* io::repeat() 创建一个无限重复一个字节的异步读取器实例。
* io::split() 将实现的单个值拆分AsyncRead + AsyncWrite为单独 的AsyncRead和AsyncWrite句柄。
* io::stderr() 标准错误
* io::stdout() 标准输出
* * io::stdin() 标准输入


```rust
use tokio::io::{self, AsyncWriteExt};

#[tokio::main]
async fn main() -> io::Result<()> {
    let mut stderr = io::stderr();
    stderr.write_all(b"Print some error here.").await?;
    Ok(())
}
```




## 数据帧 Frame

通过帧可以将字节流转换成帧组成的流。每个帧就是一个数据单元，例如客户端发送的一次请求就是一个帧。

<details>
<summary>展开查看</summary>
<pre>

<code>
use mini_redis::{Frame, Result};
use mini_redis::frame::Error::Incomplete;
use bytes::Buf;
use std::io::Cursor;
fn parse_frame(&mut self) -> Result<Option<Frame>> {
let mut buf = Cursor::new(&self.buffer[..]); // 创建 `T: Buf` 类型
    // 检查是否读取了足够解析出一个帧的数据
    match Frame::check(&mut buf) {
        Ok(_) => {
            // 获取组成该帧的字节数
            let len = buf.position() as usize;

            // 在解析开始之前，重置内部的游标位置
            buf.set_position(0);

            // 解析帧
            let frame = Frame::parse(&mut buf)?;

            // 解析完成，将缓冲区该帧的数据移除
            self.buffer.advance(len);

            // 返回解析出的帧
            Ok(Some(frame))
        }
        // 缓冲区的数据不足以解析出一个完整的帧
        Err(Incomplete) => Ok(None),
        // 遇到一个错误
        Err(e) => Err(e.into()),
    }
}
</code></pre>
</details>


# 并发代码的其他执行方法

## select!

在实际使用时，一个重要的场景就是同时等待多个异步操作的结果，并且对其结果进行进一步处理，
在本章节，我们来看看，强大的 select! 是如何帮助咱们更好的控制多个异步操作并发执行的。

```rust
use tokio::sync::oneshot;

async fn some_operation() -> String {
    // 在这里执行一些操作...
}
#[tokio::main]
async fn main() {
    let (tx1, rx1) = oneshot::channel();
    let (tx2, rx2) = oneshot::channel();

    tokio::spawn(async {
        let _ = tx1.send("one");
    });

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

    // 任何一个 select 分支结束后，都会继续执行接下来的代码
}
```

## 在循环中使用 select!

```rust
use tokio::sync::mpsc;

#[tokio::main]
async fn main() {
    let (tx1, mut rx1) = mpsc::channel(128);
    let (tx2, mut rx2) = mpsc::channel(128);
    let (tx3, mut rx3) = mpsc::channel(128);

    loop {
        let msg = tokio::select! {
            Some(msg) = rx1.recv() => msg,
            Some(msg) = rx2.recv() => msg,
            Some(msg) = rx3.recv() => msg,
            else => { break }
        };

        println!("Got {}", msg);
    }

    println!("All channels have been closed.");
}
```

在循环中使用 select! 最大的不同就是，当某一个分支执行完成后，select! 会继续循环等待并执行下一个分支，直到所有分支最终都完成，最终匹配到 else 分支，然后通过 break 跳出循环

# Stream

要使用 stream ，目前还需要手动引入对应的包：

> tokio-stream = "0.1

## 迭代
目前， Rust 语言还不支持异步的 for 循环，因此我们需要 while let 循环和 StreamExt::next() 一起使用来实现迭代的目的:

```rust
use tokio_stream::StreamExt;

#[tokio::main]
async fn main() {
    let mut stream = tokio_stream::iter(&[1, 2, 3]);
    
    while let Some(v) = stream.next().await {
        println!("GOT = {:?}", v);
    }
}
```
## 适配器

迭代器有两种适配器：

* 迭代器适配器，会将一个迭代器转变成另一个迭代器，例如 map，filter 等
* 消费者适配器，会消费掉一个迭代器，最终生成一个值，例如 collect 可以将迭代器收集成一个集合

```rust
use tokio_stream::StreamExt;
use mini_redis::client;

async fn publish() -> mini_redis::Result<()> {
    let mut client = client::connect("127.0.0.1:6379").await?;

    // 发布一些数据
    client.publish("numbers", "1".into()).await?;
    client.publish("numbers", "two".into()).await?;
    client.publish("numbers", "3".into()).await?;
    client.publish("numbers", "four".into()).await?;
    client.publish("numbers", "five".into()).await?;
    client.publish("numbers", "6".into()).await?;
    Ok(())
}

async fn subscribe() -> mini_redis::Result<()> {
    let client = client::connect("127.0.0.1:6379").await?;
    let subscriber = client.subscribe(vec!["numbers".to_string()]).await?;
    let messages = subscriber.into_stream();
    
    let messages = messages
        .filter(|msg| match msg {
            Ok(msg) if msg.content.len() == 1 => true,
            _ => false,
        })
        .map(|msg| msg.unwrap().content)
        .take(3);

    tokio::pin!(messages);

    while let Some(msg) = messages.next().await {
        println!("got = {:?}", msg);
    }

    Ok(())
}

#[tokio::main]
async fn main() -> mini_redis::Result<()> {
    tokio::spawn(async {
        publish().await
    });

    subscribe().await?;

    println!("DONE");

    Ok(())
}
```


