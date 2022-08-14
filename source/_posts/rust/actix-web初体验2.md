---
title: actix-web初体验-其二
tags:
  - rust
  - io
  - web
categories: Rust
abbrlink: 1375505722
date: 2022-08-13 21:24:25
---

[https://actix.rs/](https://actix.rs/docs/errors/)

<!-- more -->

按着官网的顺序走吧

# 处理错误 Error

将 Result 强制转换为 HTTP 响应
```rust
impl<T: Responder, E: Into<Error>> Responder for Result<T, E>
```

自定义错误
```rust
// 定义错误的类型
#[derive(Debug, Display, Error)]
pub enum UserError {
    #[display(fmt = "An internal error occurred. Please try again later.")]
    InternalError,

    #[display(fmt = "bad request")]
    BadClientData,

    #[display(fmt = "timeout")]
    Timeout,
}
// 定义错误的状态码和处理方式
impl error::ResponseError for UserError {
    fn status_code(&self) -> StatusCode {
        match *self {
            UserError::InternalError => StatusCode::INTERNAL_SERVER_ERROR,
            _ => StatusCode::BAD_GATEWAY
        }
    }

    fn error_response(&self) -> HttpResponse {
        HttpResponse::build(self.status_code())
            .insert_header(ContentType::html())
            .body(self.to_string())
    }
}
// 使用错误
#[get("/cats/{id}")]
pub async fn cats1(info: web::Path<Info>) -> Result<&'static str, UserError> {
    match info.id {
        1 => Err(UserError::InternalError),
        2 => Ok("success"),
        _ => Err(UserError::BadClientData)
    }
}
```

# 添加简单守卫

[https://actix.rs/docs/url-dispatch/](https://actix.rs/docs/url-dispatch/)
```rust
fn main() {
    App::new().service(
        web::resource("/path").route(
            web::route()
                .guard(guard::Get())
                .guard(guard::Header("content-type", "text/plain"))
                .to(HttpResponse::Ok),
        ),
    )
}
```
ResourceHandler::route()返回一个Route对象。可以使用类似构建器的模式配置路由。可以使用以下配置方法：

* Route::guard()注册一个新的守卫。可以为每条路线注册任意数量的警卫。
* Route::method()注册了一个方法守卫。可以为每条路线注册任意数量的警卫。
* Route::to()为这个路由注册了一个异步处理函数。只能注册一个处理程序。通常处理程序注册是最后的配置操作。

# 生成资源 URL

使用HttpRequest.url_for()方法根据资源模式生成 URL
url_for()方法返回Url 对象，因此您可以修改此 url（添加查询参数、锚点等）。
```rust
#[get("/test/")]
async fn index(req: HttpRequest) -> Result<HttpResponse> {
    let url = req.url_for("foo", &["1", "2", "3"])?; // <- generate url for "foo" resource

    Ok(HttpResponse::Found()
        .insert_header((header::LOCATION, url.as_str()))
        .finish())
}
```

# 规范化路由

[https://actix.rs/docs/url-dispatch/](https://actix.rs/docs/url-dispatch/)

通过规范化它意味着：

* 在路径中添加尾部斜杠。
* 用一个替换多个斜杠。

```rust
#[actix_web::main]
async fn main() -> std::io::Result<()> {
    use actix_web::{web, App, HttpServer};

    HttpServer::new(|| {
        App::new()
            .wrap(middleware::NormalizePath::default())
            .route("/resource/", web::to(index))
    })
        .bind(("127.0.0.1", 8080))?
        .run()
        .await
}
```

# 返回 JSON

使用 <code>web::Json</code> 即可

```rust
use actix_web::{get, web, Responder, Result};
use serde::Serialize;

#[derive(Serialize)]
struct MyObj {
    name: String,
}

#[get("/a/{name}")]
async fn index(name: web::Path<String>) -> Result<impl Responder> {
    let obj = MyObj {
        name: name.to_string(),
    };
    Ok(web::Json(obj))
}

async fn current_temperature() -> impl Responder {
    web::Json(json!({ "temperature": 42.3 }))
}
```

# 内容解码

支持以下编码格式

* **Brotli**
* **Gzip**
* **Deflate**
* **Identity**
```rust
use actix_web::{get, middleware, App, HttpResponse, HttpServer};

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new()
            .wrap(middleware::Compress::default())
            // .wrap(middleware::Compress::new(ContentEncoding::Br)) // 单独设置
            .service(index_br)
    })
    .bind(("127.0.0.1", 8080))?
    .run()
    .await
}
```

使用 <code>ContentEncoding::Identity</code> 来显式禁用压缩内容
```rust
#[get("/")]
async fn index() -> HttpResponse {
    HttpResponse::Ok()
        // v- disable compression
        .insert_header(ContentEncoding::Identity)
        .body("data")
}
```


# 中间件

[https://actix.rs/docs/middleware/](https://actix.rs/docs/middleware/)
通常，中间件涉及以下操作：

* 预处理请求
* 后处理响应
* 修改应用状态
* 访问外部服务（redis、日志记录、会话）


# 简单的静态资源服务

```rust
use actix_files as fs;
use actix_web::{App, HttpServer};

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new().service(
            fs::Files::new("/static", ".")
                .show_files_listing()
                .use_last_modified(true),
        )
    })
    .bind(("127.0.0.1", 8080))?
    .run()
    .await
}
```