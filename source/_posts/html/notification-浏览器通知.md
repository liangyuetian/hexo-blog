---
title: notification-浏览器通知
abbrlink: 310135269
date: 2019-09-30 14:08:35
tags:
categories:
---

推送通知实际上包括了两件独立的事情：
* 使用 Push API 发送消息
* 使用 Notification API 显示通知。


<!-- more -->
[https://www.ituring.com.cn/book/tupubarticle/24767](https://www.ituring.com.cn/book/tupubarticle/24767)

### Notification API
```js
Notification.requestPermission().then(function(permission){
  if (permission === "granted") {
    new Notification("Shiny");
  }
});
```

只需用这段示例代码就可以请求显示通知的权限。然后，如果权限被授予（granted），就创建一个标题为 Shiny 的通知。就这么简单。

```js
if (Notification.permission === "granted") {
    showNotification();
} else if (Notification.permission === "denied") {
    console.log("Can't show notification");
} else if (Notification.permission === "default") {
  Notification.requestPermission().then(function(permission) {
    if (permission === "granted") {
        // 被调用后，显示权限许可对话框，用户选择同意；
        // 被调用，但由于用户之前已经授权过，所以不需再显示权限许可对话框。
        showNotification();
    } else if (Notification.permission === "denied") {
        // 被调用后，显示权限许可对话框，但用户选择拒绝
        // 但由于用户之前已经拒绝过，所以不需再显示权限许可对话框。
        console.log("Can't show notification");
    } else if (Notification.permission === "default") {
        // 被调用后，显示权限许可对话框，但用户没有做出选择，直接关闭了对话框。
        console.log("Can't show notification, but can ask for permission again.");
    }
  });
}
```

> #### 同源策略
> 用户的选择是根据同源策略保存的。换句话说，一旦用户授予权限，你就可以在应用中同源的任何页面下创建新的通知。
>如果两个 Web 页面的 URI 协议（例如 HTTPS、HTTP）、主机名（例如 www.talater.com）和端口号都一致，那么这两个页面就是同源的。例如，在 https://www.talater.com/annyang 授予的权限，可以在 https://www.talater.com/upup 上使用，但是不能在 http://www.talater.com/annyang（使用 HTTP，而权限授予在 HTTPS 协议上）或者https://www.talater.com:8443/（端口号不一致）上使用。


### push Api

Push API 允许用户同意应用推送消息，让服务器可以随时推送消息到浏览器。这些消息会由 service worker 监听并处理，甚至在用户离开应用后也可以进行操作。最常见的操作方式就是向用户显示通知。

这给应用带来了巨大的能量。一旦你可以在任何时候向用户的设备发送消息，你就有可能用无尽的消息来骚扰他。你甚至可以通过每隔几秒向 service worker 发送消息，然后将一些影响数据响应发回服务器，来静默地跟踪用户的行为。

为了确保 Push API 不会被这样滥用，所有的推送消息都要通过中心消息服务器。中心服务器由浏览器供应商维护，它会为你跟踪所有用户的订阅。它确保推送消息不会被滥用，且用户不会被骚扰。即使用户在你发送消息时无法触达，它也能确保消息被到达。

