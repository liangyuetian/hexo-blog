---
title: docker-构建镜像
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
abbrlink: 25f30fec
date: 2019-07-05 13:38:50
tags: docker
categories: Docker
---

```bash
vim Dockerfile 
```

<!-- more -->

```bash
# 首先创建Dockerfile文件
vim Dockerfile 

FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html

docker build -t nginx:v3 .              
# -t让docker分配一个伪终端并绑定到容器的标准输入上, -i则让容器的标准输入保持打开.

docker build https://github.com/twang2218/gitlab-ce-zh.git#:11.1
# 这行命令指定了构建所需的 Git repo，并且指定默认的 master 分支，构建目录为 /11.1/，然后 Docker 就会自己去 git clone 这个项目、切换到指定分支、并进入到指定目录后开始构建。

docker build - < Dockerfile
# 或
cat Dockerfile | docker build -
# 从标准输入中读取 Dockerfile 进行构建

docker build - < context.tar.gz
# 从标准输入中读取上下文压缩包进行构建
```
