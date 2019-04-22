---
title: angular - ngTemplateOutlet 与 ContenChildren
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
tags:
  - angular
  - ngTemplateOutlet
  - ContenChildren
categories: Angular
abbrlink: 4568
date: 2019-03-28 23:21:14
---

angular ContentChild 与 ContenChildren 使用方法
[官网文档 ContentChild](https://www.angular.cn/api/core/ContentChild)
[官网文档 ContentChildren](https://www.angular.cn/api/core/ContentChildren)
[官网文档 QueryList](https://www.angular.cn/api/core/QueryList)

<!-- more -->

### 首先介绍 ngTemplateOutlet 
官方说法：根据一个提前备好的 TemplateRef 插入一个内嵌视图。
个人理解，就是将被<code>ng-template</code>标签包裹的元素渲染到某一个地方，一个更细粒度的内容投影（ng-content）
比如常规使用方法
```ts
@Component({
    selector: 'ng-template-outlet-example',
    template: `
        <ng-container *ngTemplateOutlet="greet; context: myContext""></ng-container>
        <!-- ng-container 只是一个容器，常用与分组，不渲染到页面上，很安全 -->
        <!-- 注意不要跟 ng-content 弄混，ng-content是内容投影 -->
        <!-- ngTemplateOutlet可以具有自己的定义域 可以用angular特殊的content语法来定义；跟vue的作用于插槽作用相同  -->

        <ng-template #greet><span>Hello</span></ng-template>
        <!-- ng-template 是定义一组模板，可以通过ViewChild获取实例(TemplateRef) -->
    `
})
export class NgTemplateOutletExample {
    myContext = {$implicit: 'World', localSk: 'Svet'};
}
```
常规的用法！！
那如果这个<code>ng-template</code>标签是用户定义的呢，比如从父组件传入

### ContentChild 与 ngTemplateOutlet
ContentChild 用于获取从父组件传入的模板，ngTemplateOutlet用于将模板显示到视图中

不得不说，从这里就可以看出anglar的api设计真的非常颗粒化，任意组合，功能复杂的同时也不失灵活度

写法如下
#### 首先 定义子组件
```ts
import {
    Component,
    OnInit,
    AfterViewInit,
    ContentChild,
    TemplateRef
} from '@angular/core';

@Component({
    selector: 'order-list',
    template: `
        <div>
            <ng-container *ngTemplateOutlet="template; context: myContext"></ng-container>
            <!-- 这里的template只是变量名，与class中对应 -->
        </div>
    `
})
export class OrderListComponent implements OnInit, AfterViewInit {
    myContext = {$implicit: 'World', localSk: 'Svet'};

    @ContentChild(TemplateRef, {read: TemplateRef}) template: TemplateRef<{ $implicit: number; timer: number }>;
    // 只需一行

    ngAfterViewInit() {
        console.log('template:', this.template);
    }
    
    ngOnInit() {
    }
}
```
#### 其次 父组件使用
```ts
import {
    Component,
    OnInit,
    AfterViewInit,
    ContentChild,
    TemplateRef
} from '@angular/core';

@Component({
    selector: 'order-list',
    template: `
        <div>
            <order-list>
                <ng-template let-person="localSk"><span>Ahoy0 {{person}}!</span></ng-template>
                <!-- 没错，这里只能使用ng-template包裹，因为ngTemplateOutlet只是获取模板的，如果是组件的话，有更好用的方案-->
            </order-list>
        </div>
    `
})
export class OrderListComponent {}
```
特别简单，核心只有3部
```html
<!-- 子组件模板定义-->
<ng-container *ngTemplateOutlet="greet; context: myContext""></ng-container>
```
```ts
// 子组件 获取未来模板
@ContentChild(TemplateRef, {read: TemplateRef}) template: TemplateRef<{ $implicit: number; timer: number }>;
```
```html
<!-- 父组件使用 -->
<order-list>
    <ng-template let-person="localSk"><span>Ahoy0 {{person}}!</span></ng-template>
</order-list>
```

### ContentChildren 与 ngTemplateOutlet
目前ContentChild只能接受一个<code>ng-template</code>组件，那么想写多个怎么办
于是，ContentChildren出现了

#### 子组件中
```ts
@Component({
    selector: 'ng-template-outlet-example',
    template: `
        <div *ngFor="let template of templates">
            <ng-container *ngTemplateOutlet="template; context: myContext"></ng-container>
        </div>
        <!-- for 循环，哈哈 -->
    `
})
export class NgTemplateOutletExample {
    myContext = {$implicit: 'World', localSk: 'Svet'};
    @ContentChildren(TemplateRef, {read: TemplateRef}) templates !: QueryList<any>;
    // ContentChildren可以将ng-template解析为一个数组，类型是QueryList，是一个塞满TemplateRef的集合，可以使用for of 来遍历
    // 也可以使用使用 templates.forEach templates.map 来遍历，支持常用的数组方法，如：map filte find reduce forEach some
    // 也可以使用  templates.toArray() templates.toString() 来转化成数组或者字符串
}
```
#### 父组件

```ts
@Component({
    selector: 'order-list',
    template: `
        <div>
            <order-list>
                <ng-template let-person="localSk"><span>Ahoy0 {{person}}!</span></ng-template>
                <ng-template let-person="localSk"><span>Ahoy1 {{person}}!</span></ng-template>
                <ng-template let-person="localSk"><span>Ahoy2 {{person}}!</span></ng-template>
                <ng-template let-person="localSk"><span>Ahoy3 {{person}}!</span></ng-template>
                <ng-template let-person="localSk"><span>Ahoy4 {{person}}!</span></ng-template>
            </order-list>
        </div>
    `
})
export class OrderListComponent {}
```
核心代码为：
```ts
@ContentChildren(TemplateRef, {read: TemplateRef}) templates !: QueryList<any>;
```
