---
title: git-重写历史提交记录
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-07-05 11:31:28
tags: git
categories: Git
---

重写历史提交记录

<!-- more -->

### 第一步
执行 git 命令, 修改近几次的信息
```bash
git rebase -i HEAD~3
```
{% asset_img 1.jpg %}
得到如上的提交信息，值得注意的是： 提交信息是倒叙排列的

### 第二步
我们将需要修改的那条记录前面的pick 修改成edit
{% asset_img 2.jpg %}
{% asset_img 3.jpg %}
得到如下结果
{% asset_img 4.jpg %}
然后按着做就行了
如果想修改用户邮箱
则执行
```bash
git commit --amend --author="user <email@outlook.com>"
```
### 撤销
如果哪里写错了，则执行 git rebase --abort 重来一遍
```bash
git rebase --abort
```

参考资料[https://www.jianshu.com/p/0f1fbd50b4be](https://www.jianshu.com/p/0f1fbd50b4be)
参考资料[https://blog.csdn.net/furzoom/article/details/90670893](https://blog.csdn.net/furzoom/article/details/90670893)
