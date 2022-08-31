---
title: rust可变引用的排他性
categories: Rust
abbrlink: 222756652
date: 2022-08-19 10:27:27
tags:
---

[https://rust-book.junmajinlong.com/ch6/04_understand_mutable_ref.html](https://rust-book.junmajinlong.com/ch6/04_understand_mutable_ref.html)

大佬这个解释我一下子就明白了

<!-- more -->

**可以将可变引用看作是一把独占锁。在当前作用域内，从第一次使用可变引用开始创建这把独占锁，之后无论使用原始变量(即所有权拥有者)、可变引用还是不可变引用都会抢占这把独占锁，以保证只有一方可以访问数据，每次抢得独占锁后，都会将之前所有引用变量给锁住(原始变量依然可用)，使它们变成不可用状态。当离开当前作用域时，当前作用域内的所有独占锁都被释放。**


