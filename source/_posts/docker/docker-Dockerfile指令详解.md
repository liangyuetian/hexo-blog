---
title: docker-Dockerfile指令详解
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
abbrlink: 357215418
date: 2019-07-05 13:43:58
tags:
categories:
---

Dockerfile 指令详解
FROM，RUN，，COPY, ADD等

<!-- more -->

### COPY 复制文件
```bash
COPY package.json /usr/src/app/ # 复制package.json到/usr/src/app/
COPY hom* /mydir/
COPY hom?.txt /mydir/
COPY --chown=55:mygroup files* /mydir/
COPY --chown=bin files* /mydir/
COPY --chown=1 files* /mydir/
COPY --chown=10:11 files* /mydir/
# 在使用该指令的时候还可以加上 --chown=<user>:<group> 选项来改变文件的所属用户及所属组。
```
### ADD 更高级的复制文件
```bash
# ADD 指令和 COPY 的格式和性质基本一致。但是在 COPY 基础上增加了一些功能。
# 如果 <源路径> 为一个 tar 压缩文件的话，压缩格式为 gzip, bzip2 以及 xz 的情况下，ADD 指令将会自动解压缩这个压缩文件到 <目标路径> 去。
FROM scratch
ADD ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /
ADD --chown=10:11 files* /mydir/ 同样可以设置权限
```
### CMD 容器启动命令
```bash
# CMD 指令就是用于指定默认的容器主进程的启动命令的。
CMD echo $HOME # 
CMD ["nginx", "-g", "daemon off;"] # 接执行 nginx 可执行文件，并且要求以前台形式运行。
```
### ENV 定义环境变量
```bash
ENV NODE_VERSION 7.2.0

RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
  && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
  && grep " node-v$NODE_VERSION-linux-x64.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -xJf "node-v$NODE_VERSION-linux-x64.tar.xz" -C /usr/local --strip-components=1 \
  && rm "node-v$NODE_VERSION-linux-x64.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs
```

### VOLUME 定义匿名卷

```bash
VOLUME /data
# 这里的 /data 目录就会在运行时自动挂载为匿名卷，任何向 /data 中写入的信息都不会记录进容器存储层，从而保证了容器存储层的无状态化。当然，运行时可以覆盖这个挂载设置

docker run -d -v mydata:/data xxxx
# 在这行命令中，就使用了 mydata 这个命名卷挂载到了 /data 这个位置，替代了 Dockerfile 中定义的匿名卷的挂载配置。
```
### EXPOSE 声明端口 
要将 EXPOSE 和在运行时使用 -p <宿主端口>:<容器端口> 区分开来。-p，是映射宿主端口和容器端口，
换句话说，就是将容器的对应端口服务公开给外界访问，而 EXPOSE 仅仅是声明容器打算使用什么端口而已，并不会自动在宿主进行端口映射。

### WORKDIR 指定工作目录



抄的
参考资料[https://yeasy.gitbooks.io/docker_practice/content/image/dockerfile/copy.html](https://yeasy.gitbooks.io/docker_practice/content/image/dockerfile/copy.html)
