---
layout: post
title: Docker学习笔记 - 搭建私有镜像仓库
category: Docker
tags: [Docker]
---

Docker镜像仓库简单来说就是存储Docker镜像的地方，就像git仓库一样。

Docker官方提供了[Docker Hub](https://hub.docker.com/)来存储我们的镜像文件。由于[Docker Hub](https://hub.docker.com/)管理的都是公开的镜像，并且访问速度特别的慢，所以我们打算来搭建一个私有的仓库，需要使用官方库中的registry镜像。

## 构建私有Docker镜像仓库

### 拉取registry镜像 

```
docker pull registry
```

### 运行registry镜像 

```
docker run -d -p 5000:5000 -v /data/registry:/var/lib/registry registry
```

- `-d`参数表示后台运行
- `-P`参数将容器的5000端口映射到宿主机的5000端口
- `-v`参数将宿主机中/data/registry目录挂载到容器中/var/lib/registry

