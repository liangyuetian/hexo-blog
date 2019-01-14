---
title: electron-app实例方法
playlist:
  - name: 随便～随便～随便～（Cover RIN&REN）
    artist: Hanser / YUKIri
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: //p2.music.126.net/9GAbSb_hlXPu66HWInJOww==/109951162846052486.jpg?param=90y90
date: 2019-01-14 23:06:39
tags: electron
categories:
---

app 对象具有以下方法:
文档：[https://electronjs.org/docs/api/app#appquit](https://electronjs.org/docs/api/app#appquit)

<!-- more -->

### app.quit()
尝试关闭所有窗口 将首先发出 before-quit 事件。 如果所有窗口都已成功关闭, 则将发出 will-quit 事件, 并且默认情况下应用程序将终止。

此方法会确保执行所有beforeunload 和 unload事件处理程序。 可以在退出窗口之前的beforeunload事件处理程序中返回false取消退出。

### app.exit([exitCode])
立即退出程序并返回 exitCode。exitCode 的默认值是 0。

所有窗口都将立即被关闭（不会弹出询问提示），而且 before-quit 和 will-quit 事件也不会被触发

### app.relaunch([options])
options Object (可选)

args String
execPath String (可选)
从当前实例退出，重启应用。

默认情况下，新的实例会和当前实例使用相同的工作目录以及命令行参数。 当设置了 args 参数时， args 将作为命令行参数传递。 当设置了 execPath ，execPath 将被执行以重新启动，而不是当前的应用程序。

请注意, 此方法在执行时不会退出当前的应用程序, 你需要在调用 app.relaunch 方法后再执行 app. quit 或者 app.exit 来让应用重启。

当 app.relaunch 被多次调用时,多个实例将在当前实例退出后启动。

立即重启当前实例并向新的实例添加新的命令行参数的示例：
```js
const { app } = require('electron')

app.relaunch({ args: process.argv.slice(1).concat(['--relaunch']) })
app.exit(0)
```

### app.focus()
在 Linux 系统中, 使第一个可见窗口获取焦点。在 macOS 上, 让该应用成为活动应用程序。在 Windows 上, 使应用的第一个窗口获取焦点。

### app.hide() macos
隐藏所有的应用窗口，不是最小化.

### app.show() macOS
显示所有被隐藏的应用窗口。需要注意的是，这些窗口不会自动获取焦点。

### app.getAppPath()
返回 String 类型 - 当前应用程序所在目录

### app.getVersion()
返回 String-加载的应用程序的版本。 如果应用程序的 package. json 文件中找不到版本号, 则返回当前包或者可执行文件的版本。

### app.getName()
返回 String-当前应用程序的名称, 它是应用程序的 package. json 文件中的名称。

根据 npm 的命名规则, 通常 package.json 中的 name 字段是一个短的小写字符串。 通常还应该指定一个 productName 字段, 是首字母大写的完整名称，用于表示应用程序的名称。Electron 会优先使用这个字段作为应用名。

### app.setName(name)
name String
设置当前应用程序的名字

### app.getLocale()
返回 string——当前应用程序的语言环境。可能的返回值被记录在这里。

要设置区域，则需要在应用启动时使用命令行时打开开关，你可以在这里找到。

注意: 分发打包的应用程序时, 你必须指定 locales 文件夹。

注意： 在 Windows 上，你必须得等 ready 事件触发之后，才能调用该方法


### app.addRecentDocument(path) macOS Windows
path String
将此 path 添加到最近打开的文件列表中

这个列表由操作系统进行管理。在 Windows 中从任务栏访问列表, 在 macOS 中通过 dock 菜单进行访问。

### app.clearRecentDocuments() macOS Windows
清空最近打开的文档列表


## app.setAsDefaultProtocolClient(protocol[, path, args])
**设置别人可以唤醒此软件的协议**
protocol String - 协议的名称, 不包含 ://。 如果您希望应用程序处理 electron:// 的链接, 请将 electron 作为该方法的参数.
pathString (可选) Windows -默认为 process.execPath
args String Windows - 默认为空数组
返回 Boolean-是否成功调用。

此方法将当前可执行文件设置为协议(也称为URI方案) 的默认处理程序。 它允许您将应用程序更深入地集成到操作系统中。 一旦注册成功, 所有 your-protocol:// 格式的链接都会使用你的程序打开。 整个链接 (包括协议) 将作为参数传递给您的应用程序。

在 Windows 系统中，你可以提供可选参数 path，可执行文件的路径和 args (在启动时传递给可执行文件的参数数组)

注意: 在 macOS 上, 您只能注册已添加到应用程序的 info. plist 中的协议, 在运行时不能对其进行修改。 但是，您可以在构建时使用简单的文本编辑器或脚本更改文件。 有关详细信息，请参阅 Apple's documentation

API 在内部使用 Windows 注册表和 LSSetDefaultHandlerForURLScheme。


### app.enableSandbox() Experimental macOS Windows
在应用程序上启用完全沙盒模式。

这个方法只能在应用程序准备就绪（ready）之前调用。

### app.enableMixedSandbox() Experimental macOS Windows
在应用程序上启用混合沙盒模式。

这个方法只能在应用程序准备就绪（ready）之前调用。

### app.isInApplicationsFolder() macOS
返回 Boolean- 应用程序当前是否在系统应用程序文件夹运行。 可以搭配 app. moveToApplicationsFolder ()使用

### app.dock.bounce([type]) macOS
type String (可选) - 可以为critical 或 informational. 默认值为 informational
当传入的是 critical 时, dock 中的应用将会开始弹跳, 直到这个应用被激活或者这个请求被取消。

当传入的是 informational 时, dock 中的图标只会弹跳一秒钟。但是, 这个请求仍然会激活, 直到应用被激活或者请求被取消。

返回 Integer 这个请求的 ID

### app.dock.cancelBounce(id) macOS
id Integer
取消这个 id 对应的请求。