---
title: rocket 一些其他方法
abbrlink: 2725065807
date: 2022-08-10 00:44:58
tags:
  - rust
  - io
  - web
categories: Rust
---

[https://rocket.rs/v0.5-rc/guide/responses/#typed-uris](https://rocket.rs/v0.5-rc/guide/responses/#typed-uris)

<!-- more -->

## 类型化的 URI

构造一个url

```rust

#[get("/<id>/<name>?<age>")]
fn person(id: Option<usize>, name: &str, age: Option<u8>) { /* .. */ }

fn t () {

    // with unnamed parameters, in route path declaration order
    let mike = uri!(person(101, "Mike Smith", Some(28)));
    assert_eq!(mike.to_string(), "/101/Mike%20Smith?age=28");

    // with named parameters, order irrelevant
    let mike = uri!(person(name = "Mike", id = 101, age = Some(28)));
    assert_eq!(mike.to_string(), "/101/Mike?age=28");
    let mike = uri!(person(id = 101, age = Some(28), name = "Mike"));
    assert_eq!(mike.to_string(), "/101/Mike?age=28");

    // with a specific mount-point
    let mike = uri!("/api", person(id = 101, name = "Mike", age = Some(28)));
    assert_eq!(mike.to_string(), "/api/101/Mike?age=28");

    // 使用 _ 忽略参数 相当于 传入了 Option::None
    let mike = uri!(person(101, "Mike", _));
    assert_eq!(mike.to_string(), "/101/Mike");
    let mike = uri!(person(id = 101, name = "Mike", age = _));
    assert_eq!(mike.to_string(), "/101/Mike");
}
```

## 使用 <code>UriDisplay</code> 派生

```rust
use rocket::form::Form;

#[derive(FromForm, UriDisplayQuery)]
struct UserDetails<'r> {
    age: Option<usize>,
    nickname: &'r str,
}

#[post("/user/<id>?<details..>")]
fn add_user(id: usize, details: UserDetails) { /* .. */ }
```
