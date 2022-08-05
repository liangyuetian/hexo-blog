---
title: Background Tasks API
abbrlink: 2827174248
date: 2022-08-04 23:06:56
tags:
categories:
---

[https://developer.mozilla.org/zh-CN/docs/Web/API/Background_Tasks_API](https://developer.mozilla.org/zh-CN/docs/Web/API/Background_Tasks_API)

幕后任务协作调度 API (也叫幕后任务 API 或者简单称为 requestIdleCallback() API) 提供了由用户代理决定，在空闲时间自动执行队列任务的能力。

<!-- more -->


# 概念和用法
浏览器的主线程以其事件循环队列为中心。此代码渲染 Document 上待更新展示的内容，执行页面待运行的 JavaScript 脚本，接收来自输入设备的事件，以及分发事件给需要接收事件的元素。此外，事件循环队列处理与操作系统的交互、浏览器自身用户界面的更新等等。这是一个非常繁忙的代码块，您的主要 JavaScript 代码可能会和这些代码一起也在这个线程中执行。当然，大多数（不是所有）能够更改 DOM 的代码都在主线程中运行，因为用户界面更改通常只对主线程可用。

因为事件处理和屏幕更新是用户关注性能最明显的两种方式。对于您的代码来说，防止在事件队列中出现卡顿是很重要的。在过去，除了编写尽可能高效的代码和将尽可能多的工作移交给 workers 之外，没有其他可靠的方法可以做到这一点。 Window.requestIdleCallback() 允许浏览器告诉您的代码可以安全使用多少时间而不会导致系统延迟，从而有助于确保浏览器的事件循环平稳运行。如果您保持在给定的范围内，您可以使用户体验更好。

# 充分利用空闲回调
因为 idle callbacks 旨在为代码提供一种与事件循环协作的方式，以确保系统充分利用其潜能，不会过度分配任务，从而导致延迟或其他性能问题，因此您应该考虑如何使用它。

* 对非高优先级的任务使用空闲回调。
* 空闲回调应尽可能不超支分配到的时间。
* 避免在空闲回调中改变 DOM。
  * 如果你的回调需要改变 DOM，它应该使用Window.requestAnimationFrame()来调度它。
* 避免运行时间无法预测的任务。
* 在你需要的时候要用 timeout，但记得只在需要的时候才用。

# 方法

window.requestIdleCallback(callback[, options])

options 可选
包括可选的配置参数。具有如下属性：
timeout 如果指定了 timeout，并且有一个正值，而回调在 timeout 毫秒过后还没有被调用，那么回调任务将放入事件循环中排队，即使这样做有可能对性能产生负面影响。


```js
// 创建后台任务

var handle = window.requestIdleCallback(callback)

// 取消后台任务
window.cancelIdleCallback(handle);
```

