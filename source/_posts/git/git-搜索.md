---
title: git-搜索
tags: Git
categories: Git
abbrlink: 1414556024
date: 2020-02-18 10:56:56
---

## Git 搜索

[参考文献](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E6%90%9C%E7%B4%A2)

### 在commit记录中搜索
```bash
git log --grep eslint # 在commit 中搜索 eslint 关键词
git log --grep --oneline eslint # 在 commit 中搜索 eslint 关键词, 简化输出信息
git log --grep eslint --author liangyuetian@outlook.com # 在commit 中搜索 eslint 关键词,并且筛选提交人 
git log -S eslint # 查看关键字是什么时候引入的，可以使用 -S 选项来显示新增和删除该字符串的提交。
git log -S eslint --oneline # 简化输出信息
git log -L :countDownDiff:date.js # 查看这个文件下，countDownDiff函数的变更记录
```

### 在commit记录中的文件中搜索
```
git grep eslint # 在 文件 中搜索关键词
git grep -n eslint # -n 显示行号
git grep --count 'pay_box' # --count 简要的输出，只输出文件路径
git grep -p 'pay_box' # -p 输出前后字符，用来判断这个关键字的类型
```