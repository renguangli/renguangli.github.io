---
layout: post
title: 搭建 Docker 私有镜像仓库
category: Docker
tags: [Docker]
---

Docker镜像仓库简单来说就是存储Docker镜像的地方，就像git仓库一样。

Docker官方提供了[Docker Hub](https://hub.docker.com/)来存储我们的镜像文件。由于[Docker Hub](https://hub.docker.com/)管理的都是公开的镜像，并且访问速度特别的慢，所以有时需要我们搭建一个私有镜像仓库。

## 构建私有Docker镜像仓库

搭建私有Docker镜像仓库需要使用官方库中的registry镜像

### 拉取registry镜像 

``` bash
[root@localhost ~]# docker pull registry
Using default tag: latest
Trying to pull repository docker.io/library/registry ... 
latest: Pulling from docker.io/library/registry
d6a5679aa3cf: Pull complete 
ad0eac849f8f: Pull complete 
2261ba058a15: Pull complete 
f296fda86f10: Pull complete 
bcd4a541795b: Pull complete 
Digest: sha256:5a156ff125e5a12ac7fdec2b90b7e2ae5120fa249cf62248337b6d04abc574c8
Status: Downloaded newer image for docker.io/registry:latest
```

### 运行registry镜像 

``` bash
[root@localhost data]# docker run -d -p 5000:5000 -v /data/registry:/var/lib/registry registry
ca9baaf6e3aa755c2ec04722bbc4b415c85ac9bcd04bf574c29b96b16b18051d
```

- `-d`参数表示后台运行
- `-p`参数表示将容器5000端口映射到宿主机端口5000
- `-v`参数表示容器目录/var/lib/registry映射到宿主机目录/data/registry

运行成功返回容器id

### 查看运行着的registry容器

``` bash
[root@localhost data]# docker ps |grep registry
ca9baaf6e3aa        registry            "/entrypoint.sh /e..."   About a minute ago   Up About a minute   0.0.0.0:5000->5000/tcp   condescending_volhard
```

至此，Docker私有镜像仓库搭建完毕，让我们push一个Docker镜像吧。

以tomcat镜像为例，首先给tomcat镜像添加标签

``` bash
[root@localhost data]# docker tag tomcat 192.168.66.88:5000/tomcat:latest
[root@localhost data]# docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
192.168.66.88:5000/tomcat      latest              41a54fe1f79d        3 weeks ago         463 MB
docker.io/tomcat          latest              41a54fe1f79d        3 weeks ago         463 MB
docker.io/registry        latest              2e2f252f3c88        3 weeks ago         33.3 MB
```

将192.168.66.88:5000/tomcat镜像push到私有仓库
``` bash
[root@localhost data]# docker push 192.168.66.88:5000/tomcat:latest
The push refers to a repository [192.168.66.88:5000/tomcat]
Get https://192.168.66.88:5000/v1/_ping: http: server gave HTTP response to HTTPS client
```

这里我们需要开放https的协议

```
root@localhost data]# vi /etc/docker/daemon.json
# 添加配置
{"insecure-registries":["192.168.66.100:5000"]} 
# 重启Docker服务
[root@localhost data]# systemctl restart docker.service 
# 启动镜像仓库
[root@localhost data]# docker run -d -p 5000:5000 -v /data/registry:/var/lib/registry registry
```

这时候在push到镜像仓库

```
[root@localhost data]# docker push 192.168.66.88:5000/tomcat
The push refers to a repository [192.168.66.88:5000/tomcat]
2eda9150fd94: Pushed 
4b9367441797: Pushed 
b43708ebead1: Pushed 
f3a3749a49fd: Pushed 
00fc55ade1b8: Pushed 
034f354793a4: Pushed 
a8d68e0ce4e3: Pushed 
e0caf225fa20: Pushed 
da22ca4fb8f3: Pushed 
2eb1c9bfc5ea: Pushed 
0b703c74a09c: Pushed 
b28ef0b6fef8: Pushed 
latest: digest: sha256:e7bff3f32561daf3ba91142bca7c5aeaf49c376ee07e0d6b66e3207aa08a343a size: 2836
```

查看上传的镜像

``` bash
[root@localhost data]# docker exec -it fe18d4d5ea23 ls /var/lib/registry/docker/registry/v2/repositories
tomcat
```

在看看镜像仓库容器映射到本机上的目录,可以看到映射的文件夹目录有tomcat的镜像数据了

```
[root@localhost data]# ll /data/registry/docker/registry/v2/repositories/
total 0
drwxr-xr-x. 5 root root 52 Oct  5 00:45 tomcat
```

删除本地tomcat镜像，然后在从私有仓库拉取tomcat镜像

```
[root@localhost data]# docker rmi 192.168.66.88:5000/tomcat:latest
Untagged: 192.168.66.88:5000/tomcat:latest
Untagged: 192.168.66.88:5000/tomcat@sha256:e7bff3f32561daf3ba91142bca7c5aeaf49c376ee07e0d6b66e3207aa08a343a
[root@localhost data]# docker pull 192.168.66.88:5000/tomcat
Using default tag: latest
Trying to pull repository 192.168.66.88:5000/tomcat ... 
latest: Pulling from 192.168.66.88:5000/tomcat
Digest: sha256:e7bff3f32561daf3ba91142bca7c5aeaf49c376ee07e0d6b66e3207aa08a343a
Status: Downloaded newer image for 192.168.66.88:5000/tomcat:latest
```

可以看到从私有仓库拉取tomcat镜像成功


