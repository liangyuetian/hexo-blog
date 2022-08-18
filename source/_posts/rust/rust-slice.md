---
title: rust slice 切片方法
abbrlink: 2392828224
date: 2022-08-19 00:49:47
tags:
categories:
---

[https://doc.rust-lang.org/std/primitive.slice.html#impl](https://doc.rust-lang.org/std/primitive.slice.html#impl)

<!-- more -->

一些常见方法: 

1. len(): 取slice元素个数
2. is_empty(): 判断slice是否为空
3. first(): 返回切片的第一个元素
4. last(): 返回切片的最后一个元素
5. first_mut(): 返回指向切片第一个元素的可变指针
6. first_mut(): 返回指向切片第一个元素的可变指针 <code>Some((first, elements)) = x.split_first()</code>
7. first_mut(): 返回切片的第一个和所有其余元素
8. split_last(): 返回切片的最后一个和所有其余元素 
9. contains(): 判断是否包含某个元素
10. get(): 根据索引的类型返回对元素或子切片的引用。v.get(1) v.get(0..2)
11. repeat(): 重复slice指定次数
12. reverse(): 反转slice
13. join(): 将各元素压平(flatten)并通过指定的分隔符连接起来
14. swap(): 交换两个索引处的元素，如s.swap(1,3)
15. windows(): 以指定大小的窗口进行滚动迭代
16. starts_with(): 判断slice是否以某个slice开头
17. iter(): 返回切片上的迭代器
18. is_ascii(): 检查此切片中的所有字节是否都在 ASCII 范围内
19. trim_ascii_start(): 返回删除了前导和尾随 ASCII 空白字节的字节切片。
20. trim_ascii_end()
21. trim_ascii()
22. flatten(): 接受 a &[[T; N]]，并将其展平为 a &[T]。
23. flatten_mut(): 接受 a &mut [[T; N]]，并将其展平为 a &mut [T]。

