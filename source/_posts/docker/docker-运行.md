---
title: docker-运行
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
abbrlink: c0124e2
date: 2019-07-05 12:18:51
tags: docker
categories: Docker
---

docker run 

<!-- more -->


```bash
docker -it --rm \
ubuntu:18.04 \
bash
```

```bash
cat /etc/os-release
```

docker run 就是运行容器的命令，具体格式我们会在 容器 一节进行详细讲解，我们这里简要的说明一下上面用到的参数。

-it：这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端。

--rm：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。

ubuntu:18.04：这是指用 ubuntu:18.04 镜像为基础来启动容器。

bash：放在镜像名后的是 命令，这里我们希望有个交互式 Shell，因此用的是 bash。
进入容器后，我们可以在 Shell 下操作，执行任何所需的命令。

这里，我们执行了 cat /etc/os-release，这是 Linux 常用的查看当前系统版本的命令，从返回的结果可以看到容器内是 Ubuntu 18.04.1 LTS 系统。

最后我们通过 exit 退出了这个容器。
