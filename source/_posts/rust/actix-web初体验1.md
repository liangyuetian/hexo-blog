---
title: actix-web初体验-其一
tags:
  - rust
  - io
  - web
categories: Rust
abbrlink: 1526986460
date: 2022-08-11 23:29:49
---

<code>actix-web</code> 也是一款非常快速和成熟的方法，为什么看了 rocket 还要看 actix-web 呢，那是因为 rocket 不支持 webSocket 呀

[https://actix.rs/](https://actix.rs/)

<!-- more -->

# 简单创建

```rust
use actix_web::{get, post, web, App, HttpResponse, HttpServer, Responder};

#[get("/")]
async fn hello() -> impl Responder {
    HttpResponse::Ok().body("Hello world!")
}

#[post("/echo")]
async fn echo(req_body: String) -> impl Responder {
    HttpResponse::Ok().body(req_body)
}

async fn manual_hello() -> impl Responder {
    HttpResponse::Ok().body("Hey there!")
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    println!("启动服务：http://127.0.0.1:7003");
    HttpServer::new(|| {
        App::new()
            .app_data(web::Data::new(AppState {
                app_name: String::from("Actix Web"),
            }))
            .service(hello) // 使用宏来定义路由
            .service(echo)
            .route("/hey", web::get().to(manual_hello)) // 绑定普通函数
            .workers(4) // 使用 workers 设置多线程，默认是逻辑 CPU 的数量
    })
        .keep_alive(KeepAlive::Os) // 使用 keep_alive 来 配置 长链接
        .shutdown_timeout() // 优雅关闭的超时时间,默认30s
        .bind(("127.0.0.1", 7003))?
        .run()
        .await
}
```

# 设置路由统一前缀和分组

* 使用 <code>web::scope</code> 来给所有的请求添加前缀
* 使用 configure 和 web::ServiceConfig 来将不同的请求分到不同的模块中

每个都ServiceConfig可以有自己的data,routes和services。

```rust
use actix_web::{web, App, HttpResponse, HttpServer};

// this function could be located in a different module
fn scoped_config(cfg: &mut web::ServiceConfig) {
    cfg.service(
        web::resource("/test")
            .route(web::get().to(|| async { HttpResponse::Ok().body("test") }))
            .route(web::head().to(HttpResponse::MethodNotAllowed)),
    );
}

// this function could be located in a different module
fn config(cfg: &mut web::ServiceConfig) {
    cfg.service(
        web::resource("/app")
            .route(web::get().to(|| async { HttpResponse::Ok().body("app") }))
            .route(web::head().to(HttpResponse::MethodNotAllowed)),
    );
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new()
            .configure(config)
            .service(web::scope("/api").configure(scoped_config))
            .route(
                "/",
                web::get().to(|| async { HttpResponse::Ok().body("/") }),
            )
    })
    .bind(("127.0.0.1", 8080))?
    .run()
    .await
}
```

# 完全自定义响应

1. 派生 Serialize
2. 为 struct 实现 Responder 特征
3. return struct

```rust

use serde::Serialize;
use serde_json;

#[derive(Serialize)]
struct MyObj {
    name: &'static str,
}

// Responder
impl Responder for MyObj {
    type Body = BoxBody;

    fn respond_to(self, _req: &HttpRequest) -> HttpResponse<Self::Body> {
        let body = serde_json::to_string(&self).unwrap();

        // Create response and set content type
        HttpResponse::Ok()
            .content_type(ContentType::json())
            .body(body)
    }
}

async fn index() -> impl Responder {
    MyObj { name: "user" }
}

// .route("/index", web::get().to(index))
```

# 动态路由

简单匹配，同时拥有类型约束
```rust
#[get("/users/{user_id}/{friend}")] // <- define path parameters
async fn index(path: web::Path<(u32, String)>) -> Result<String> {
    let (user_id, friend) = path.into_inner();
    Ok(format!("Welcome {}, user_id {}!", friend, user_id))
}

// 或者 

#[derive(Deserialize)]
struct Info {
    user_id: u32,
    friend: String,
}

/// extract path info using serde
#[get("/users2/{user_id}/{friend}")] // <- define path parameters
async fn users2(info: web::Path<Info>) -> Result<String> {
    Ok(format!(
        "Welcome users2 {}, user_id {}!",
        info.friend, info.user_id
    ))
}

// 手动处理

#[get("/users/{user_id}/{friend}")] // <- define path parameters
async fn users3(req: HttpRequest) -> Result<String> {
    let name: String = req.match_info().get("friend").unwrap().parse().unwrap();
    let userid: i32 = req.match_info().query("user_id").parse().unwrap();

    Ok(format!("Welcome {}, user_id {}!", name, userid))
}
```

# 获取 query 参数

使用  web::Query<Info> 来获取

```rust
#[derive(Deserialize)]
struct Info {
    username: String,
}


#[get("/")]
async fn index(info: web::Query<Info>) -> String {
    format!("Welcome {}!", info.username)
}
```
# 获取 body 正文

使用  web::Json<Info> 来获取

```rust
#[derive(Deserialize)]
struct Info {
    username: String,
}


#[get("/")]
async fn index(info: web::Json<Info>) -> String {
    format!("Welcome {}!", info.username)
}
```
# 获取 表单数据

用于接收 Content-Type: x-www-form-urlencoded 类型的参数

使用  web::Form<Info> 来获取

```rust
#[derive(Deserialize)]
struct FormData {
    username: String,
}

/// extract form data using serde
/// this handler gets called only if the content type is *x-www-form-urlencoded*
/// and the content of the request could be deserialized to a `FormData` struct
#[post("/")]
async fn index(form: web::Form<FormData>) -> Result<String> {
    Ok(format!("Welcome {}!", form.username))
}
```