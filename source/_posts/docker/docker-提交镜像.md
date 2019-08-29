---
title: docker-提交镜像
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
tags: docker
categories: Docker
abbrlink: 2298398267
date: 2019-07-05 13:35:59
---

```bash
docker exec webserve bash # 进入镜像
```
<!-- more -->

```bash
docker exec webserve bash # 进入镜像
vim ... # 修改镜像
docker diff webserve # 对比镜像改动
docker commit --author "王闯" --message "修改了默认网页" webserve nginx:v2 # 提交镜像
docker image ls 查看镜像

# --author 是指定修改的作者，而 --message 则是记录本次修改的内容。这点和 git 版本控制相似，不过这里这些信息可以省略留空。

docker history nginx:v2 # 查看镜像内的历史记录
docker run --name web2 -d -p 81:80 nginx:v2 # 运行这个镜像
```
