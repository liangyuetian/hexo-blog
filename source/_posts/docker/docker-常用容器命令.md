---
title: docker-常用容器命令
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
abbrlink: 578569c8
date: 2019-07-05 13:34:24
tags: docker
categories: Docker
---

```bash
docker ps -a 列出容器
```

<!-- more -->

```bash
docker ps -a 列出容器
docker run --name webserve -d -p 5003:80 nginx  创建容器   本地端口:容器端口
docker start webserve 启动容器
docker exec -i -t  mynginx bash 进入容器
docker container stop webserve 停止容器
docker container restart webserve 重启容器
docker container rm webserve 删除容器
docker container logs webserve 打印容器日志 docker logs webserve
```
