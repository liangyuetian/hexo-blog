---
title: rxjs总结2
abbrlink: 3220494565
date: 2022-08-25 22:36:22
tags:
  - rxjs
  - 操作符
categories: RxJs
---

https://rxjs.dev/api/index/function/finalize

<!-- more -->

上一年写nodejs有个复杂的订阅任务，需要在各个阶段打日志，
嗯，在各个阶段都打上日志后，终于进行到最后一步，在订阅结束后打一个结束的日志，
然后尴尬了，翻遍了操作符，没有找到相关的可以用的操作符，然后问问java的同事:你们的RxJava有结束的操作符吗？回曰：toCompleted()

rxjs就弱一点，没有对应的操作符，然后咋办呢，于是又创建了一个Observers专门用于监听订阅流结束，虽然实现了功能，也很不错，但是总不是那么优雅，
最近又在刷rxjs，惊喜的发现，缺失的这个操作符居然已经有了：<code>finalize</code>

真是不错！

在前端的各种生态中，框架，库层出不穷，然而最让我感动的还是 [Rxjs](https://rxjs.dev/);真是太赞了，它只是一个处理异步流程的库，可是却让我如此的开心，用它来组织你的异步任务，简直无可挑剔。

从rxjs5开始用，再在nestjs用rxjs6，到现在7,rxjs8居然也要发布，真是蒸蒸日上。

不说了，我去再看一遍