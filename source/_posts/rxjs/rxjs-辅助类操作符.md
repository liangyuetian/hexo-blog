---
title: 辅助类操作符
date: 2019-01-02 23:11:01
tags: 
    - rxjs
    - 操作符
playlist:
  -
    name: 千里邀月（人声本家）
    artist: 泠鸢yousa / 茶理理 / Hanser / 三无MarBlue
    url: "//music.163.com/song/media/outer/url?id=497215230.mp3"
    cover: "//p2.music.126.net/4zluTz1ckuBWIbWHzc5xQA==/109951163736812658.jpg?param=90y90"
---

<img src="//p2.music.126.net/4zluTz1ckuBWIbWHzc5xQA==/109951163736812658.jpg?param=90y90" width = "100" height = "100" div align=right style="position: absolute; right: 0; margin-top: -10px;" />

辅助类操作符
count，max，min，Reduce
every，find，findIndex
isEmpty，defaultEmpty

<!-- more -->

## 第三大类：辅助类操作符

| 操作符 | 功能 |
| ------ | ------ |
| count | 统计数据流中产生的所有数据个数 |
| max min | 获得数据流中的最大和最小数据 |
| Reduce | 对数据流中所有数据进行规约操作 |
| every | 判断一个数据流是否不包含任何数据 |
| find findIndex | 找到第一个满足判定条件的数据 |
| isEmpty | 判断一个数据流是否不包含任何数据 |
| defaultEmpty | 如果一个数据流为空就默认产生一个指定的数据 |

例子：
#### 数学类操作符
#### count max min reduce 
所有的这些操作符都是实例操作符，还有一个共同特点，就是就些操作符必定会遍历上游Observable对象中突出的所有数据才给下游传递数据，也就是时说，他们就有在上游完结的时候，才给下游传递唯一数据
```js
count 的作用就是统计上游Observable对象吐出的所有数据个数。
max,min的作用就是取得上游Observable吐出所有数据的最大值或最小值，第二个参数接受一个比较函数
reduce 规约统计，跟原生数组的reduce作用一样，用法一样

```

#### every find findIndex
```js
every 要求一个判定函数作为参数，上游Observable吐出的每一个数据都会被这个判定函数检验，如果所有数据的判定结果都是true,那么上游Observable对象完结的时候，如果有一个为false，那么立刻完结吐出false,基本和原生数组every方法作用一致

```

#### isEmpty
```js
isEmpty 用于检查一个上游Observable对象是不是‘空的’，所谓‘空的’Observable是指没有吐出任何数据就完结的Observable对象。
```

#### defaultEmpty 接受一个默认值作为参数，如果发现上游是空的，那么返回这个默认值