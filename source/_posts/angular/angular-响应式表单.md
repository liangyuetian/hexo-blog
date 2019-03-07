---
title: angular-响应式表单
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-03-04 23:18:54
tags: angular
categories:
---


<!-- more -->

总结：
```ts
import {FormGroup, FormControl, FormBuilder, FormArray, Validators} from '@angular/forms';

export class ProfileEditorComponent implements OnInit {
    constructor(private fb: FormBuilder) {
    }
    // profileForm = new FormGroup({
    //     firstName: new FormControl(''),
    //     lastName: new FormControl(''),
    //     address: new FormGroup({
    //         street: new FormControl(''),
    //         city: new FormControl(''),
    //         state: new FormControl(''),
    //         zip: new FormControl('')
    //     })
    // });
    profileForm = this.fb.group({
        firstName: ['', Validators.required], // 第一项值是初始化值 第二项和第三项提供同步和异步验证器
        lastName: [''],
        address: this.fb.group({
            street: [''],
            city: [''],
            state: [''],
            zip: ['']
        }),
        aliases: this.fb.array([
            this.fb.control('')
        ])
    });

    updateProfile() {
        // 当点击按钮时，profileForm 模型中只有 firstName 和 street 被修改了。注意，street 是在 address 属性的对象中被修改的。
        // 这种结构是必须的，因为 patchValue() 方法要针对模型的结构进行更新。patchValue() 只会更新表单模型中所定义的那些属性。
        this.profileForm.patchValue({
            firstName: 'Nancy',
            address: {
                street: '123 Drew Street'
            }
        });
    }

    get aliases() {
        return this.profileForm.get('aliases') as FormArray;
    }

    addAlias() {
        this.aliases.push(this.fb.control(''));
    }

    ngOnInit() {
        console.log(this.aliases);
    }
}
```
```html
<form [formGroup]="profileForm" (ngSubmit)="onSubmit()">
    <label> First Name: <input type="text" formControlName="firstName" required/></label>
    <label>Last Name: <input type="text" formControlName="lastName"></label>
    <div formGroupName="address">
        <h3>Address</h3>
        <label> Street: <input type="text" formControlName="street"></label>
        <label> City: <input type="text" formControlName="city"></label>
        <label> State: <input type="text" formControlName="state"></label>
        <label> Zip Code: <input type="text" formControlName="zip"></label>
    </div>
    <button type="submit" [disabled]="!profileForm.valid">Submit</button>
</form>
```
## 步骤
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


### FormBuilder 来生成表单控件
* 1 导入FormBuilder控件
```ts
import { FormBuilder } from '@angular/forms';
```
* 2 注入服务
```ts
constructor(private fb: FormBuilder) { }
```
* 3 生成表单控件
```ts
// 这里，每个控件名对应的值都是一个数组，这个数组中的第一项是其初始值。
// 你可以只使用初始值来定义控件，但是如果你的控件还需要同步或异步验证器，那就在这个数组中的第二项和第三项提供同步和异步验证器。
profileForm = this.fb.group({
    firstName: [''], // 第一项值是初始化值 第二项和第三项提供同步和异步验证器
    lastName: [''],
    address: this.fb.group({
        street: [''],
        city: [''],
        state: [''],
        zip: ['']
    }),
});

// 与其对应的是完全手动创建
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

### 简单表单验证
* 1 导入验证器函数
```ts
import { Validators } from '@angular/forms';
```
* 2 - 把字段设为必填（required）
```ts
profileForm = this.fb.group({
    firstName: ['', Validators.required], // 第一项值是初始化值 第二项和第三项提供同步和异步验证器
    lastName: [''],
    address: this.fb.group({
        street: [''],
        city: [''],
        state: [''],
        zip: ['']
    }),
});

```
#### 显示表单状态
```html
<p>
    Form Status: {{ profileForm.status }}
</p>
```

### 使用表单数组管理动态控件
```ts
import { FormArray } from '@angular/forms';
```
你可以通过把一组（从零项到多项）控件定义在一个数组中来初始化一个 FormArray。为 profileForm 添加一个 aliases 属性，把它定义为 FormArray 类型。

使用 FormBuilder.array() 方法来定义该数组，并用 FormBuilder.control() 方法来往该数组中添加一个初始控件。

```ts

profileForm = this.fb.group({
    aliases: this.fb.array([
        this.fb.control('')
    ])
});

// 得到 aliases
get aliases() {
    return this.profileForm.get('aliases') as FormArray;
}

// 在 FormArray 中添加值
addAlias() {
    this.aliases.push(this.fb.control(''));
}
```
在模板中使用和显示这些值
```html
<form [formGroup]="profileForm" (ngSubmit)="onSubmit()">
    <div formArrayName="aliases">
        <h3>Aliases</h3>
        <button (click)="addAlias()">Add Alias</button>
        <div *ngFor="let address of aliases.controls; let i=index">
            <!-- The repeated alias template -->
            <label>
                Alias:
                <input type="text" [formControlName]="i">
                <!-- *ngFor 指令对 aliases FormArray 提供的每个 FormControl 进行迭代。
                因为 FormArray 中的元素是匿名的，所以你要把索引号赋值给 i 变量，并且把它传给每个控件的 formControlName 输入属性。 -->
            </label>
        </div>
    </div>
    <button type="submit" [disabled]="!profileForm.valid">Submit</button>
</form>

```