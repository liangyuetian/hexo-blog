---
title: 看rocket怎么返回json
abbrlink: 2866793110
date: 2022-08-09 23:35:19
tags:
categories:
---

官方文档定义 response 是一个 responder
[https://rocket.rs/v0.5-rc/guide/responses/#responder](https://rocket.rs/v0.5-rc/guide/responses/#responder)

<!-- more -->

## 修改 状态码 status

```rust
use rocket::response::status;

#[get("/cats")]
pub fn cats1() -> status::Accepted<String> {
    status::Accepted(Some(format!("hello cats"))) // Response code: 202 (Accepted);
}
```

返回自定义的json

```rust
#[get("/cats2")]
pub fn cats2() -> status::Custom<content::RawJson<&'static str>> {
    status::Custom(Status::Ok, content::RawJson("{ \"hi\": \"cats2\" }"))
}

// 做简单的封装
#[derive(Responder)]
#[response(status = 201, content_type = "json")]
pub struct RawTeapotJson(&'static str);

#[get("/cats3")]
pub fn cats3() -> RawTeapotJson {
    RawTeapotJson("{ \"hi\": \"cats3\" }")
}
```

其他的一些响应体
[https://rocket.rs/v0.5-rc/guide/responses/#rocket-responders](https://rocket.rs/v0.5-rc/guide/responses/#rocket-responders)

* NamedFile- 将文件流式传输到客户端；根据文件的扩展名自动设置 Content-Type。
* Redirect- 将客户端重定向到不同的 URI。
* content- 包含覆盖 Content-Type 响应的类型。
* status- 包含覆盖响应状态代码的类型。
* Flash- 设置访问时删除的“flash”cookie。
* Json- 自动将值序列化为 JSON。
* MsgPack- 自动将值序列化为 MessagePack。
* Template- 使用把手或 Tera 渲染动态模板。


## 返回的结果有可能是错误的
```rust
use std::path::{Path, PathBuf};
use rocket::fs::NamedFile;

// 使用 Option，直接返回404，即 Option::None
#[get("/cats6/<file..>")]
pub async fn cats6(file: PathBuf) -> Option<NamedFile> {
    NamedFile::open(Path::new("static/").join(file)).await.ok()
}

// 使用 Result 返回错误信息
#[get("/cats7/<file..>")]
pub async fn cats7(file: PathBuf) -> Result<NamedFile, NotFound<String>> {
    let path = Path::new("static/").join(file);
    NamedFile::open(&path).await.map_err(|e| NotFound(e.to_string()))
}

```

## 自由的返回json
```rust
use rocket::serde::{Serialize, Deserialize};
use rocket::serde::json::{Json, Value};
use rocket::serde::json::serde_json::json;

#[derive(Serialize, Deserialize)]
#[serde(crate = "rocket::serde")]
pub struct Task {
    id: u16,
    message: String
}

#[get("/cats8")]
pub fn cats8() -> Json<Task> {
    Json(Task {
        id: 0,
        message: "cats8 的 id 为 0".to_string()
    })
}

#[get("/cats9")]
pub fn cats9() -> Value {
    json!({ "status": "ok", "id": "9", "name": "cats9" })
}

```
