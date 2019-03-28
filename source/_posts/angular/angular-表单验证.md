---
title: angular-表单验证
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-03-07 23:37:55
tags: angular
categories: Angular
---

表单验证
[https://www.angular.cn/guide/form-validation](https://www.angular.cn/guide/form-validation)

<!-- more -->


### 响应式表单验证

```ts
profileForm = this.fb.group({
    firstName: [
        '', // 默认值
        [ // 同步验证器
            Validators.required,
            Validators.minLength(4),
            (control: AbstractControl): { [key: string]: any } | null => {
                if (control.value === 'wangchuang') {
                    return null;
                } else if (control.value === '大帅锅') {
                    return null;
                }
            }
        ],
        [ // 异步验证器，只有同步验证器执行完后才会执行异步
            (control: AbstractControl): { [key: string]: any } | null => {
                return new Promise((resolve, reject) => {
                    console.log('哈哈哈');
                    window.setTimeout(() => {
                        if (control.value === '哈哈哈哈哈哈') {
                            resolve(null);
                        }
                    }, 3000);
                });
            }
        ]
    ] // 第一项值是初始化值 第二项和第三项提供同步和异步验证器
});
```
或
```ts
profileForm = new FormGroup({
    firstName: new FormControl(
        '', // 默认值
        [ // 同步验证器
            Validators.required,
            Validators.minLength(4),
            (control: AbstractControl): { [key: string]: any } | null => {
                if (control.value === 'wangchuang') {
                    return null;
                } else if (control.value === '大帅锅') {
                    return null;
                }
            }
        ],
        [ // 异步验证器，只有同步验证器执行完后才会执行异步
            (control: AbstractControl): { [key: string]: any } | null => {
                return new Promise((resolve, reject) => {
                    console.log('哈哈哈');
                    window.setTimeout(() => {
                        if (control.value === '哈哈哈哈哈哈') {
                            resolve(null);
                        }
                    }, 3000);
                });
            }
        ]
    ) // 第一个值为初始值 第二个值为同步验证器，第三个值为异步验证器
});
```

### 添加到模板驱动表单 的 验证器
在模板驱动表单中，你不用直接访问 FormControl 实例。所以不能像响应式表单中那样把验证器传进去，而应该在模板中添加一个指令。
Angular 在验证过程中能识别出指令的作用，是因为指令把自己注册成了 NG_VALIDATORS 提供商，该提供商拥有一组可扩展的验证器。
```ts
providers: [{provide: NG_VALIDATORS, useExisting: ForbiddenValidatorDirective, multi: true}]
```
实现
```ts
@Directive({
  selector: '[appForbiddenName]',
  providers: [{provide: NG_VALIDATORS, useExisting: ForbiddenValidatorDirective, multi: true}]
})
export class ForbiddenValidatorDirective implements Validator {
  @Input('appForbiddenName') forbiddenName: string;

  validate(control: AbstractControl): {[key: string]: any} | null {
    return this.forbiddenName ? forbiddenNameValidator(new RegExp(this.forbiddenName, 'i'))(control)
                              : null;
  }
}
```
使用
```html
<input id="name" name="name" class="form-control"
      required minlength="4" appForbiddenName="bob"
      [(ngModel)]="hero.name" #name="ngModel" >
```
你可能注意到了自定义验证器指令是用 useExisting 而不是 useClass 来实例化的。注册的验证器必须是这个 ForbiddenValidatorDirective 实例本身，也就是表单中 forbiddenName 属性被绑定到了"bob"的那个。如果用 useClass 来代替 useExisting，就会注册一个新的类实例，而它是没有 forbiddenName 的。


### 跨字段交叉验证。。。
[https://www.angular.cn/guide/form-validation#cross-field-validation](https://www.angular.cn/guide/form-validation#cross-field-validation)


### 表示控件状态的 CSS 类
像 AngularJS 中一样，Angular 会自动把很多控件属性作为 CSS 类映射到控件所在的元素上。你可以使用这些类来根据表单状态给表单控件元素添加样式。目前支持下列类：

* .ng-valid 校验成功
* .ng-invalid 校验失败
* .ng-pending 校验中
* .ng-pristine 原始的样子
* .ng-dirty 被修改过
* .ng-untouched 没有被碰触过
* .ng-touched 被碰触过