---
title: rocket 备忘录
tags:
  - rust
  - io
  - web
categories: Rust
abbrlink: 3418369411
date: 2022-08-08 23:44:31
---

Rocket 是 Rust 的异步 Web 框架，专注于可用性、安全性、可扩展性和速度。
[https://github.com/SergioBenitez/Rocket](https://github.com/SergioBenitez/Rocket)
[https://rocket.rs/v0.5-rc/guide/overview/#overview](https://rocket.rs/v0.5-rc/guide/overview/#overview)

<!-- more -->

## 看如果 创建一个简单的请求
```rust
#[macro_use] extern crate rocket;

#[get("/")]
pub fn index() -> &'static str {
    "Hello, world!"
}

#[rocket::main]
async fn main() -> Result<(), rocket::Error> {

    let _rocket = rocket::build()
        .mount("/", routes![index])
        .launch()
        .await?;

    Ok(())
}
```

## 路由的常用配置

```rust

#[get("/")]
pub fn index() -> &'static str {
    "Hello, world!"
}

#[get("/word")]
pub fn word() -> &'static str {
    "world!"
}

// 路由匹配时过滤类型
#[get("/hello/<name>/<age>/<cool>")]
pub fn hello(name: &str, age: u8, cool: bool) -> String {
    if cool {
        format!("You're a cool {} year old, {}!", age, name)
    } else {
        format!("{}, we need to talk about your coolness.", name)
    }
}

#[get("/foo/<_>/bar", rank = 1)] // rank 设置排名
pub fn foo_bar() -> &'static str {
    "Foo _____ bar!"
}

// 匹配所有
#[get("/<_..>")]
pub fn everything() -> &'static str {
    "Hey, you're here."
}

#[get("/delay/<seconds>")]
pub async fn delay(seconds: u64) -> String {
    sleep(Duration::from_secs(seconds)).await;
    format!("Waited for {} seconds", seconds)
}

// 异步线程 和 js 的 async 差不多
#[get("/blocking_task")]
pub async fn blocking_task() -> io::Result<Vec<u8>> {
    // In a real app, use rocket::fs::NamedFile or tokio::fs::File.
    let vec = spawn_blocking(|| std::fs::read("data.txt")).await
        .map_err(|e| io::Error::new(io::ErrorKind::Interrupted, e))??;

    Ok(vec)
}

// 重定向
#[get("/admin", rank = 3)]
pub fn admin_panel_redirect() -> Redirect {
    Redirect::to(uri!(login))
}

// 获取 cookie
#[get("/user_id")]
pub fn user_id(cookies: &CookieJar<'_>) -> Option<String> {
    cookies.get_private("user_id")
        .map(|crumb| format!("User ID: {}", crumb.value()))
}

#[get("/login")]
pub fn login() -> String {
    String::from("login page")
}
// 删除 cookie
#[post("/logout")]
pub fn logout(cookies: &CookieJar<'_>) -> Flash<Redirect> {
    cookies.remove_private(Cookie::named("user_id"));
    Flash::success(Redirect::to("/"), "Successfully logged out.")
}

```

## 给所有的参数设置类型

```rust

// 接受比较复杂的参数
// 定义参数类型
#[derive(FromForm)]
pub struct User<'r> {
    name: &'r str,
    active: bool,
}

#[get("/?hello&<id>&<user..>")]
pub fn user(id: usize, user: User<'_>) {
    // hello&\
    // name=Bob+Smith&\
    // id=1337&\
    // active=yes\
    assert_eq!(id, 1337);
    assert_eq!(user.name, "Bob Smith");
    assert_eq!(user.active, true);
}
```