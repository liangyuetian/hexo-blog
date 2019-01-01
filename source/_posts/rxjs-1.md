---
title: rxjs 简介
date: 2019-01-01 23:09:18
tags: rxjs
---

函数式编程，Observable 和 Observer 
观察者模式，迭代器模式
操作符

<!-- more -->

# RxJs简介

### 首先，函数式编程
顾名思义,函数式编程就是非常强调使用函数来解决问题的一种编程方式。
要求以下几点：
* 声明式
* 纯函数
* 数据不可变性

### Observable 和 Observer 
* observable: 可以被观察的对象，即可被观察者
* observer: 观察者

### RxJS中的数据流就是Observable对象，Observable实现了下面两种设计模式：
* 观察者模式
* 迭代器模式

### Observable 的核心在于各种各样的操作符
操作符分为以下几大类：
* 创建操作符
* 合并操作符
* 辅助类操作符
* 过滤操作符
* 转化操作符
* 异常错误处理操作符

### 还有
* 多播
* 调度器(Scheduler)
* 高阶Observable
* rxjs的调试和测试