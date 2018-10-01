---
layout: post
title: Docker学习笔记 - 容器
category: Docker
tags: [Docker]
---

容器是docker的另一个核心概念，简单来说容器就是镜像的一个运行示例。

## 查看容器

使用`docker ps`命令查看运行着的容器

``` 
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                NAMES
229929eb660f        renguangli/nginx:1.0   "nginx -g 'daemon off"   17 hours ago        Up 3 seconds        0.0.0.0:80->80/tcp   nginx
```

使用`docker ps -a`命令查看所有的容器

```
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                NAMES
229929eb660f        renguangli/nginx:1.0   "nginx -g 'daemon off"   17 hours ago        Up 3 seconds        0.0.0.0:80->80/tcp   nginx
3ceb621f4aa9        jenkins             "/bin/tini -- /usr/lo"   About an hour ago   Exited (137) 48 seconds ago                            elated_davinci
```

使用`docker ps -q`命令查看运行着的容器Id

```
[root@localhost ~]# docker ps -a
CONTAINER ID       
229929eb660f        
3ceb621f4aa9        
```

使用`docker create image:tag name`命令创建一个容器并给容器一个name，例如

```
[root@localhost ~]# docker create nginx:latest nginx
fd47cedfcce5cd2879ef65b342b1cd4130cbcc081cec532ff7af1744ad589116
```

如果不加name的话随机分配一个名字

## 启动容器

使用`docker start 容器名称或容器ID`来启动一个容器，例如

```
[root@localhost ~]# docker ps -a
229929eb660f        renguangli/nginx:1.0   "nginx -g 'daemon off"   17 hours ago        Exited (0) 8 seconds ago                        nginx
[root@localhost ~]# docker start nginx 或者 docker start 229929eb660f
```

## 新建并启动容器

创建容器后可以用`docker start`命令启动容器，也可以用`docker run`命令直接新建并启动容器，`docker run`相当于先执行`docker create`命令在执行`docker start`命令。例如，输出一个"hello docker"

```
[root@localhost ~]# docker run ubuntu echo "hello docker"
hello docker
```

这跟在本次执行`echo "hello docker"`几乎没有任何区别，但是在执行`docker run`命令时经历了复杂的操作：

- 检查本地是否存在指定的镜像，，不存在就从共有仓库下载
- 利用镜像创建并启动一个容器
- 分配一个文件系统给容器，并在只读的镜像层外面挂在一个可读写层
- 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中
- 从王桥的地址池中配置一个IP地址给容器
- 执行用户指定的应用程序
- 执行完毕后容器自动终止

`docker run -d`命令会让容器在后台运行

`docker logs`命令可以查看容器日志

`docker logs -f`命令可以像`tail -f`命令一样查看容器日志

## 终止容器

使用`docker stop 容器名或容器ID`命令停止一个容器

```
[root@localhost html]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
16555d4ebdf0        nginx:1.10.0        "nginx -g 'daemon off"   47 minutes ago      Up 8 seconds        0.0.0.0:80->80/tcp   jolly_goodall
[root@localhost html]# docker stop 16555d4ebdf0 或者 docker stop jolly_goodall
```

> docker stop命令首先想容器发送SIGKILL的信号，等待一段时间后（默认10秒）在发送SIGKILL信号来终止容器
> docker kill 命令会直接发送SIGKILL信号来终止容器

终止的容器可以使用`docer start`来启动一个容器

运行着的容器可以使用`docker restart`来重启容器

## 参考资料

Docker中文社区: <http://www.docker.org.cn/>

Docker技术入门与实战