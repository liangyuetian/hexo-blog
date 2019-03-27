---
title: vue-重复使用scoped-slot
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-02-28 23:58:04
tags:
categories:
---

vue-重复使用scoped-slot
```js
render: (h, ctx) => { // dom渲染函数
    return h('div', ctx.injections.Root.$scopedSlots['icon']({ // 得到名为icon的作用域插槽
        data: ctx.props.data, // 给slot的组件传递props
        options: ctx.props.options
    }));
}
```

<!-- more -->

## 话不投机半句多，直接上代码

有3步
* 第一步：创建渲染slot的组件 **重要**
* 第二步：为slot添加父组件数据（props） **重要**
* 第三步：使用

### 第一步：创建渲染slot的组件 
首选创建一个单文件组价，由于我们是使用的slot（父组件传进来的），所以，我们只需要创建js文件即可，而不用创建.vue文件
```js
// slot.js
export default {
    name: 'Slot', // slot的名字，调试使用
    functional: true, // 使组件无状态 (没有 data ) 和无实例 (没有 this 上下文)。他们用一个简单的 render 函数返回虚拟节点使他们更容易渲染。
    inject: ['Root'], // 父组件的映射 
    props: { // 此组件接受的props
        data: Object,
        options: Object
    },
    render: (h, ctx) => { // dom渲染函数
        return h('div', ctx.injections.Root.$scopedSlots['icon']({ // 得到名为icon的作用域插槽
            data: ctx.props.data, // 给slot的组件传递props
            options: ctx.props.options
        }));
    }
};
```

### 第二步：为slot添加父组件数据（props）

```js
// 导入子组件
import IconSlot from 'slot.js';

// 切换到父组件
export default {
    name: 'IconTable',
    components: {IconSlot},
     provide () { // 该对象包含可注入其子孙的属性 在这里把父组件自身传递给子组件
        return {
            Root: this
        };
    },
    data() {
        return {
            list: [],
            options: {}
        };
    }
}

```
在模板中使用
```html
<template>
    <div v-for="(item, index) in list" :key="index">
        <icon-slot :data="item" :options="options"></icon-slot>
    </div>
</template>
```


### 第三步：使用

```html
<icon-table>
    <template slot="icon" slot-scope="{ list, options }">
        <div v-if="list.length > 1"><Icon type="add"></Icon></div>
        <div v-else><Icon type="del"></Icon></div>
    </template>
</icon-table>

```

## 大功告成 总结
如果在父组件直接取<code>this.$slot</code>或<code>this.$scopedSlots</code>的话，
其实只能用一次
如果在模板中使用 slot 标签来接收的话，其实也只能只用一次

所以核心思路是；每次使用都必须重新创建VNode节点，那只能每次都使用createElement生成node,
那只有一个办法使用render渲染了，然后第一个参数创建dom后，dom附带的数据，都可以是一个对象，只要保证节点是唯一的即可，数据其实无所谓，怎么传都可以