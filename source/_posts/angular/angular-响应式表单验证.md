---
title: angular-响应式表单验证
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-03-04 23:18:54
tags:
categories:
---


<!-- more -->
### 管理控件的值
### 1 注册 ReactiveFormsModule

```ts
import { ReactiveFormsModule } from '@angular/forms';

@NgModule({
  imports: [
    // other imports ...
    ReactiveFormsModule
  ],
})
export class AppModule { }
```

### 2 - 生成并导入一个新的表单控件

```bash
ng generate component NameEditor
```

```ts
import { Component } from '@angular/core';
import { FormControl } from '@angular/forms';

@Component({
  selector: 'app-name-editor',
  templateUrl: './name-editor.component.html',
  styleUrls: ['./name-editor.component.css']
})
export class NameEditorComponent {
  name = new FormControl('');
}
```

### 3 - 在模板中注册该控件

```html
<label>
  Name:
  <input type="text" [formControl]="name">
</label>
```

#### 显示表单控件的值
```html
<p>
  Value: {{ name.value }}
  可以使用模板语法，也可以使用subscribe() 方法进行监听
</p>
```
如：
```js
export class NameEditorComponent implements OnInit {
    name = new FormControl('');
    ngOnInit() {
        this.name.valueChanges.subscribe(val => {
            console.log(val);
        });
    }
}
```

#### 替换表单控件的值
```js
updateName() {
    this.name.setValue('Nancy');
}
```


### 把表单控件分组
```bash 
ng generate component ProfileEditor
```

#### 1 - 创建 FormGroup 实例
在组件类中创建一个名叫 profileForm 的属性，并设置为 FormGroup 的一个新实例。要初始化这个 FormGroup，请为构造函数提供一个由控件组成的对象，对象中的每个名字都要和表单控件的名字一一对应。
```ts
import { Component } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';

@Component({
    selector: 'app-profile-editor',
    templateUrl: './profile-editor.component.html',
    styleUrls: ['./profile-editor.component.css']
})
export class ProfileEditorComponent {
    profileForm = new FormGroup({
      // 对此个人档案表单，要添加两个 FormControl 实例，名字分别为 firstName 和 lastName。
        firstName: new FormControl(''),
        lastName: new FormControl(''),
    });
}
```
#### 2 保存表单数据
```html
<form [formGroup]="profileForm" (ngSubmit)="onSubmit()">
```
```html
    <button type="submit" [disabled]="!profileForm.valid">Submit</button>
```
```ts
onSubmit() {
    // TODO: Use EventEmitter with form value
    console.warn(this.profileForm.value);
}
```

#### 3 表单嵌套
```html
<form [formGroup]="profileForm" (ngSubmit)="onSubmit()">
    <label>
        First Name:
        <input type="text" formControlName="firstName">
    </label>
    <label>
        Last Name:
        <input type="text" formControlName="lastName">
    </label>
    <div formGroupName="address">
        <h3>Address</h3>

        <label>
            Street:
            <input type="text" formControlName="street">
        </label>

        <label>
            City:
            <input type="text" formControlName="city">
        </label>

        <label>
            State:
            <input type="text" formControlName="state">
        </label>

        <label>
            Zip Code:
            <input type="text" formControlName="zip">
        </label>
    </div>
    <button type="submit" [disabled]="!profileForm.valid">Submit</button>
</form>
```
```ts
profileForm = new FormGroup({
    firstName: new FormControl(''),
    lastName: new FormControl(''),
    address: new FormGroup({
        street: new FormControl(''),
        city: new FormControl(''),
        state: new FormControl(''),
        zip: new FormControl('')
    })
});
```

#### 更新部分值
```ts
updateProfile() {
    this.profileForm.patchValue({
        firstName: 'Nancy',
        address: {
            street: '123 Drew Street'
        }
    });
}
```
