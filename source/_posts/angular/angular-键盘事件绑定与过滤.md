---
title: angular-键盘事件绑定与过滤
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-02-17 15:13:22
tags: angular
categories: Angular
---

方便的angular按钮绑定
如
```html
<input (keyup.enter)="keyUpSearch($event)" value="按下回车键触发">
<input (keyup.;)="keyUpSearch($event)" value="按下;号键触发">
```
<!-- more -->

官方文档：[https://www.angular.cn/guide/user-input#key-event-filtering-with-keyenter](https://www.angular.cn/guide/user-input#key-event-filtering-with-keyenter)

(keyup) 事件处理器监听每一次按键。 有时只在意回车键，因为它标志着用户结束输入。 解决这个问题的一种方法是检查每个 $event.keyCode，只有键值是回车键时才采取行动。

更简单的方法是：绑定到 Angular 的 keyup.enter 模拟事件。 然后，只有当用户敲回车键时，Angular 才会调用事件处理器。
示例：
template:
```html
<input #box (keyup.enter)="update(box.value, $event)" (blur)="update(box.value)">
```
component:
```ts
@Component({
  selector: 'app-key-up4',
})
export class KeyUpComponent_v4 {
  value = '';
  update(value: string, event: any) { this.value = value; }
}
```

常用的过滤有
```js
(keyup.enter)    // 按键并回车
(keyup.space)    // 按键并空格
(keyup.control)  // 按键并ctrl
(keyup.shift)    // 按键并shift
(keyup.alt)      // 按键并alt
(keyup.1)        // 按键1触发
(keyup.，)       // 按键,触发
(keyup.3)        // 按键并alt
```
不支持一些具有正则含义的符号，
如 <font color="red">/ ? *</font> 等
