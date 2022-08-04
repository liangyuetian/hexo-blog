---
title: TaskController
abbrlink: 4236079582
date: 2022-08-04 19:32:24
tags:
categories:
---

TaskController 接口代表一个控制器对象，可用于中止和更改一个或多个优先任务的优先级。

<!-- more -->

[https://developer.mozilla.org/en-US/docs/Web/API/TaskController](https://developer.mozilla.org/en-US/docs/Web/API/TaskController)

```js

// 创建一个 controller 对象
const controller = new TaskController({priority: 'user-blocking'});

// 只有三个优先级，如下所列（从高到低排序）：
//
// user-blocking
// 阻止用户与页面交互的任务。这包括将页面渲染到可以使用的位置，或响应用户输入。
//
// user-visible
// 对用户可见但不一定阻止用户操作的任务。这可能包括渲染页面的非必要部分，例如非必要的图像或动画。
//
// 这是默认优先级。
//
// background
// 时间不紧迫的任务。这可能包括日志处理或初始化渲染不需要的第三方库。

const signal = controller.signal

// 监听优先级状态更改
controller.signal.addEventListener('prioritychange', (event) => {
    const previousPriority = event.previousPriority;
    const newPriority = event.target.priority;
    console.log(`Priority changed from ${previousPriority} to ${newPriority}.`);
});

// 监听取消状态
controller.signal.addEventListener('abort', (event) => {
    console.log('Task aborted');
});

// 取消任务 
controller.abort()

// 修改优先级
controller.setPriority('user-blocking')

// 使用控制器的信号发布任务。
// 信号优先级设置任务的初始优先级
scheduler.postTask(() => 'Task execute', {signal: controller.signal})
    .then((taskResult) => { console.log(`${taskResult}`); }) // Aborted (wont run)
    .catch((error) => { console.log(`Catch error: ${error}`); });  // Log error




```
