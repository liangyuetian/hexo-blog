---
title: electron-初识
date: 2019-01-10 22:26:40
tags: electron
categories:
---

第一次接触这么高大上的跨平台技术，
听说 vscode 也是它写出来的呢

Electron是由Github开发，用HTML，CSS和JavaScript来构建跨平台桌面应用程序的一个开源库。

<!-- more -->

## 1 简介
Electron是由Github开发，用HTML，CSS和JavaScript来构建跨平台桌面应用程序的一个开源库。 
Electron通过将Chromium和Node.js合并到同一个运行时环境中，并将其打包为Mac，Windows和Linux系统下的应用来实现这一目的。

看过官方文档，并自己尝试了一些demo，发觉使用electron 编写任何应用程序真的只是用HTML，CSS和JavaScript来构建单页面应用程序
不要奢望可以从官网上学习到更多前端的技术，其实本质上还是前端开发，只是多了一个核心库，为：**electron**
一切跟操作系统相关的东西，都是由nodejs实现，node可以做到什么，electron就可以做到什么，只是electron提供了更方便的图像界面接口

## 2 一些可以简单实现的界面功能
### 1 渲染启动
```js
// 贼简单
// 首先引入electron
const { app, BrowserWindow } = require('electron')

// 在此监听一些生命周期钩子
app.on('ready', () => {
  // Electron 会在初始化后并准备
  // 创建浏览器窗口时，调用这个函数。
  // 部分 API 在 ready 事件触发后才能使用。
  // 在这里进行窗口创建
})

app.on('window-all-closed', () => {
  // 当全部窗口关闭时退出。
  // 在 macOS 上，除非用户用 Cmd + Q 确定地退出，
  // 否则绝大部分应用及其菜单栏会保持激活。
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('activate', () => {
  // 在macOS上，当单击dock图标并且没有其他窗口打开时，
  // 通常在应用程序中重新创建一个窗口。
  if (win === null) {
    createWindow()
  }
})

// 对app 监听是进行主进程的监听
// 还可以单独对创建的窗口进行监听 每创建一个窗口都可以被称为一个渲染进程
```

### 创建窗口
```js

// 接下来创建一个窗口
let win = new BrowserWindow({ width: 800, height: 600 })
// 然后加载应用的 index.html。或者一个远程地址 win.loadURL('http://localhost:7001')
win.loadFile('index.html')

// 打开开发者工具
win.webContents.openDevTools() // 也可以动态打开，随时执行随时打开

```

### 窗口的生命周期监听
```js
// 当窗口关闭时触发
win.on('closed', function () {
  win = null
})
```