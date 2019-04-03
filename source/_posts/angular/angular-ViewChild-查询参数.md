---
title: ViewChild ContentChild 系列的查询参数
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-04-03 22:55:41
tags:
categories:
---

ViewChild ContentChild 系列的查询参数，可以灵活的获得对应实例

<!-- more -->

### 官方说明
在调用 NgAfterViewInit 回调函数之前就会设置这些视图查询。
元数据属性：
* selector - 用于查询的指令类型或名字。
* read - 从查询到的元素中读取另一个令牌。
所支持的选择器包括：
* 任何带有 @Component 或 @Directive 装饰器的类
* 字符串形式的模板引用变量（比如可以使用 @ViewChild('cmp') 来查询 <my-component #cmp></my-component>
* 组件树中任何当前组件的子组件所定义的提供商（比如 @ViewChild(SomeService) someService: SomeService ）
* 任何通过字符串令牌定义的提供商（比如 @ViewChild('someToken') someTokenVal: any ）
* TemplateRef（比如可以用 @ViewChild(TemplateRef) template; 来查询 <ng-template></ng-template>）

代码:
```html
<div #testBox [appElementTitle]="'属性指令测试'" [appCopyAttr]="'https://liangyuetian.cn/'">
    appElementTitle 属性指令测试 appCopyAttr 属性指令测试
</div>
<div #box [appElementTitle]="'这是box的title'" [appCopyAttr]="'https://baidu.com.cn/'">
    appElementTitle 属性指令测试 appCopyAttr 属性指令测试
</div>
```
```ts
export class AppComponent implements OnInit, AfterViewInit {
    @ViewChild('testBox', {read: ElementRef}) elBox: ElementRef;
    @ViewChild('testBox', {read: CopyAttrDirective}) copy: CopyAttrDirective;
    @ViewChild('testBox', {read: ElementTitleDirective}) titles: ElementTitleDirective;

    @ViewChild('box', {read: ElementRef}) elBox2: ElementRef;
    @ViewChild('box', {read: CopyAttrDirective}) copy2: CopyAttrDirective;
    @ViewChild('box', {read: ElementTitleDirective}) titles2: ElementTitleDirective;

    keyUpSearch($event: { [key: string]: any }) {
        console.log($event.code, $event.key, $event);
    }
    ngOnInit() {
    }
    ngAfterViewInit() {
        console.log('one', this.elBox, this.copy, this.titles);
        console.log('tow', this.elBox2, this.copy2, this.titles2);
    }
}

```
效果：

{% asset_img Snipaste_2019-04-03_23-16-11.png %}