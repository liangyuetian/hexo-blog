---
title: electron-生命周期
playlist:
  - name: 千户 / 尤莉 - 雨だれの歌（少女终末旅行 插曲/第五集ED）
    artist: Kyle Xian
    url: //music.163.com/song/media/outer/url?id=517042218.mp3
    cover: //p1.music.126.net/_R0em0U-JicI5xybjZJA8Q==/109951163050383094.jpg?param=90y90
date: 2019-01-14 22:48:54
tags: electron 
categories:
---

> 控制你的应用程序的事件生命周期
> 文档地址：[https://electronjs.org/docs/api/app](https://electronjs.org/docs/api/app)

<!-- more -->

## electron 的生命周期
app 对象会发出以下事件
文档地址：[https://electronjs.org/docs/api/app](https://electronjs.org/docs/api/app)
### window-all-closed 最后一个窗口被关闭
最后一个窗口被关闭时退出应用
```js
const { app } = require('electron')
app.on('window-all-closed', () => {
  app.quit()
})
```

### read 当 Electron 完成初始化时被触发
当 Electron 完成初始化时被触发。 在 macOS 中, 如果从通知中心中启动，那么 launchInfo 中的 userInfo 包含用来打开应用程序的 NSUserNotification 信息。 你可以通过调用 app.isReady() 方法来检查此事件是否已触发。
```js
const { app } = require('electron')
app.on('read', () => {
  app.quit()
})
```

### will-finish-launching 
当应用程序完成基础的启动的时候被触发
在 Windows 和 Linux 中, will-finish-launching 事件与 ready 事件是相同的; 在 macOS 中，这个事件相当于 NSApplication 中的 applicationWillFinishLaunching 提示。 通常会在这里为 open-file 和 open-url 设置监听器，并启动崩溃报告和自动更新。

绝大部分情况下，你必须在ready事件句柄中处理所有事务。

### quit 在应用程序退出时发出
在应用程序退出时发出
注:在 Windows 系统中，如果应用程序因系统关机/重启或用户注销而关闭，那么这个事件不会被触发。
```js
const { app } = require('electron')
app.on('quit', () => {
  app.quit()
})
```
### will-quit 
当所有窗口都已关闭并且应用程序将退出时发出。调用 event. preventDefault () 将阻止终止应用程序的默认行为。
关于 window-all-closed 和 will-quit 事件之间的差异, 请参见 window-all-closed 事件的说明。
注:在 Windows 系统中，如果应用程序因系统关机/重启或用户注销而关闭，那么这个事件不会被触发。
```js
const { app } = require('electron')
app.on('will-quit', () => {
  app.quit()
})
```
### before-quit
在应用程序开始关闭窗口之前触发。 调用 event.preventDefault() 会阻止默认的行为。默认的行为是终结应用程序。
注意: 如果应用程序退出是因调用了autoUpdater. quitAndInstall (), 所有窗口都会发出close Event 然后 before-quit Event 并关闭所有窗口。

注:在 Windows 系统中，如果应用程序因系统关机/重启或用户注销而关闭，那么这个事件不会被触发。
```js
const { app } = require('electron')
app.on('before-quit', () => {
  app.quit()
})
```

### window-all-closed 当所有的窗口都被关闭时触发
如果你没有监听此事件并且所有窗口都关闭了，默认的行为是控制退出程序；但如果你监听了此事件，你可以控制是否退出程序。 如果用户按下了 Cmd + Q，或者开发者调用了 app.quit()，Electron 会首先关闭所有的窗口然后触发 will-quit 事件，在这种情况下 window-all-closed 事件不会被触发。
```js
const { app } = require('electron')
app.on('window-all-closed', () => {
  app.quit()
})
```

## 主动打开类

### open-file macos
当用户想要在应用中打开一个文件时发出。 open-file 事件通常在应用已经打开，并且系统要再次使用该应用打开文件时发出。 open-file也会在一个文件被拖到 dock 并且还没有运行的时候发出。 请确认在应用启动的时候(甚至在 ready 事件发出前) 就对 open-file 事件进行监听。

如果你想处理这个事件，你应该调用 event.preventDefault() 。

在 Windows 系统中，你需要解析 process.argv (在主进程中) 来获取文件路径

### open-url macos
当用户想要在应用中打开一个 URL 时发出。 应用程序的 Info. plist 文件必须在 CFBundleURLTypes 项中定义 url 方案, 并将 NSPrincipalClass 设置为 AtomApplication。

如果你想处理这个事件，你应该调用 event.preventDefault() 。

### activate macos
当应用被激活时发出。 各种操作都可以触发此事件, 例如首次启动应用程序、尝试在应用程序已运行时或单击应用程序的坞站或任务栏图标时重新激活它。

### new-window-for-tab macos
当用户单击 macOS 新选项卡按钮时发出。仅当当前 BrowserWindow 具有 tabbingIdentifier 时, 才会显示新的选项卡按钮

### browser-window-blur
在窗口失去焦点时触发

### browser-window-created
在创建新的 browserWindow 时发出

### web-contents-created
在创建新的 webContents 时发出。

### gpu-process-crashed
当 gpu 进程崩溃或被杀时触发

### session-created
当 Electron创建了一个新的 session后被触发.
```js
const { app } = require('electron')

app.on('session-created', (event, session) => {
  console.log(session)
})

```