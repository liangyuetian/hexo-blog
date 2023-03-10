---
title: \b 和 \B
tags: regex
categories: RegExp
abbrlink: 3545086800
date: 2019-08-26 22:43:44
---

\b 是单词边界，具体就是 \w 与 \W 之间的位置
也包括 \w 与 ^ 之间的位置，和 \w 与 $ 之间的位置。

<!-- more -->

## \b 单词边界

```js
var result = "[JS] Lesson_01.mp4".replace(/\b/g, '#');
console.log(result); 
// => "[#JS#] #Lesson_01#.#mp4#"\

```

第 1 个，两边字符是 "[" 与 "J"，是 \W 与 \w 之间的位置。
第 2 个，两边字符是 "S" 与 "]"，也就是 \w 与 \W 之间的位置。
第 3 个，两边字符是空格与 "L"，也就是 \W 与 \w 之间的位置。
第 4 个，两边字符是 "1" 与 "."，也就是 \w 与 \W 之间的位置。
第 5 个，两边字符是 "." 与 "m"，也就是 \W 与 \w之间的位置。
第 6 个，位于结尾，前面的字符 "4" 是 \w，即 \w 与 $ 之间的位置。

## \B 非单词边界

知道了 \b 的概念后，那么 \B 也就相对好理解了。
\B 就是 \b 的反面的意思，非单词边界。例如在字符串中所有位置中，扣掉 \b，剩下的都是 \B 的。
具体说来就是 \w 与 \w、 \W 与 \W、^ 与 \W，\W 与 $ 之间的位置。
比如上面的例子，把所有 \B 替换成 "#"：

```js
var result = "[JS] Lesson_01.mp4".replace(/\B/g, '#');
console.log(result);
// => "#[J#S]# L#e#s#s#o#n#_#0#1.m#p#4"

```


