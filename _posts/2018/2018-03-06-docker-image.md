---
layout: post
title: Docker 镜像常见操作
category: Docker
tags: [Docker]
---

Docker镜像常用命令介绍和使用

## 获取镜像

镜像是容器运行的前提条件，[官方Docker Hub](https://hub.docker.com/) 镜像仓库提供了10W+个镜像提供大家开放下载。

命令格式：

```bash
docker pull name:tag
```
name为镜像名称，tag为镜像的标签(通常用来表示版本信息)

获取一个Ubuntu 14.04系统镜像

``` bash
[root@localhost ~]# docker pull ubuntu:14.04
```

如果不指定tag，则默认会选择latest标签，下载仓库中最新版本的镜像。

严格地讲仓库名称还要添加镜像地址(即registry，注册服务器）为前缀，我们使用的是官方Docker Hub，所以前缀可以省略,完整命令为docker pull `docker.io/library/ubuntu:14.04`
如果下载非官方的镜像，仓库名称前要指定完整的仓库地址。

例如我们从网易的镜像源下载ubuntu 14.04 正确命令为

```
docker pull hub.c.163.com/ubuntu:14.04
```

pull子命令支持的选项主要包括`-a, --all-tags=true|false` 是否获取从那个库中的所有镜像，默认为false，具体的选项可以通过 `docker pull --help` 命令查看。有了镜像后，我们就可以以这个镜像为基础启动一个容器来运行。以上面的 ubuntu:14.04 为例，如果我们打算启动里面的 bash 并且进行交互式操作的话，可以执行下面的命令。

```
[root@localhost ~]# docker run -it --rm ubuntu:14.04 bash
root@21762fe64d8f:/# cat /etc/os-release 
NAME="Ubuntu"
VERSION="14.04.5 LTS, Trusty Tahr"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 14.04.5 LTS"
VERSION_ID="14.04"
HOME_URL="http://www.ubuntu.com/"
SUPPORT_URL="http://help.ubuntu.com/"
BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
root@21762fe64d8f:/# exit
exit
[root@localhost ~]# 
```

docker run 就是运行容器的命令，我们这里简要的说明一下上面用到的参数。

`-it`：这是两个参数，一个是 `-i`：交互式操作，一个是 `-t` 终端。我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端。`--rm`：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 `docker rm` 我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 `--rm` 可以避免浪费空间。

ubuntu:14.04：这是指用 ubuntu:14.04 镜像为基础来启动容器。

bash：放在镜像名后的命令，这里我们希望有个交互式 Shell，因此用的是 bash。

进入容器后，我们可以在 Shell 下操作，执行任何所需的命令。这里，我们执行了 `cat /etc/os-release`，这是 Linux 常用的查看当前系统版本的命令，从返回的结果可以看到容器内是 Ubuntu 14.04.5 LTS 系统。最后我们通过 `exit` 退出了这个容器。

## 查看镜像信息

### 查看本地已存在镜像
```
[root@localhost ~]# docker images 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/ubuntu    14.04               ccc7a11d65b1        4 weeks ago         120.1 MB
docker.io/ubuntu    latest              ccc7a11d65b1        4 weeks ago         120.1 MB
```
在列出的信息中有5个字段，分别是镜像来自哪个仓库、标签、镜像ID、创建时间。
- `docker images -a`列出所有镜像文件
- `docker images -q`仅列出镜像ID
- `docker images -f dangling=true`列出没有别使用的镜像

### 添加标签

```
[root@localhost ~]# docker tag  ubuntu:14.04 ubuntu:14
```

然后使用`docker images`查看镜像,多了一个拥有ubuntu:14标签的镜像

```
[root@localhost ~]# docker images 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker.io/ubuntu    14                  ccc7a11d65b1        4 weeks ago         120.1 MB
docker.io/ubuntu    14.04               ccc7a11d65b1        4 weeks ago         120.1 MB
docker.io/ubuntu    latest              ccc7a11d65b1        4 weeks ago         120.1 MB
```

### 使用`docker inspect`命令查看镜像详细信息。

包括制作者、适应架构、各层的数字摘要等。信息比较多就不放出来了

### 使用`docker history name:tag`查看镜像历史

比如查看`ubuntu:latest`镜像的创建过程

```
[root@yangjian06 wso2am]# docker history ubuntu:latest
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
ccc7a11d65b1        5 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0 B                 
<missing>           5 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo 'doc   7 B                 
<missing>           5 weeks ago         /bin/sh -c sed -i 's/^#\s*\(deb.*universe\)$/   2.759 kB            
<missing>           5 weeks ago         /bin/sh -c rm -rf /var/lib/apt/lists/*          0 B                 
<missing>           5 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /u   745 B               
<missing>           5 weeks ago         /bin/sh -c #(nop) ADD file:39d3593ea220e686d5   120.1 MB  
```

过长的命令被截断了，可以使用`--no-trunc`选项输出完整命令

## 搜索镜像

使用`docker search`命令可以搜索镜像库里中共享的镜像，默认搜索官方仓库的镜像。比如我们搜索nginx镜像

```
[root@yangjian06 wso2am]# docker search -s 40 nginx
INDEX       NAME                                               DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/nginx                                    Official build of Nginx.                        6865      [OK]       
docker.io   docker.io/jwilder/nginx-proxy                      Automated Nginx reverse proxy for docker c...   1124                 [OK]
docker.io   docker.io/richarvey/nginx-php-fpm                  Container running Nginx + PHP-FPM capable ...   439                  [OK]
docker.io   docker.io/jrcs/letsencrypt-nginx-proxy-companion   LetsEncrypt container to use with nginx as...   223                  [OK]
docker.io   docker.io/kong                                     Open-source Microservice & API Management ...   112       [OK]       
docker.io   docker.io/webdevops/php-nginx                      Nginx with PHP-FPM                              90                   [OK]
docker.io   docker.io/kitematic/hello-world-nginx              A light-weight nginx container that demons...   85                   
```

输出结果将按照星级评级进行排序，`-s`参数表示星级40以上的nginx镜像，支持的参数还有

- `--automated=true|false` 仅显示自动创建的镜像，默认为否
- `--no-trunc=true|false` 输出信息不截断提示，默认为否

## 删除镜像

### 使用标签删除镜像 

命令`docker rmi`可以删除镜像,例如我们删除ubuntu:14镜像

```
[root@localhost ~]# docker rmi ubuntu:14
Untagged: ubuntu:14
```

如果该镜像有多个标签的话删除的只是标签并不会删除镜像。如果只有一个标签的话`docker rmi`命令会彻底删除镜像。

### 使用id删除镜像

使用命令`docker rmi ID`命令可以删除镜像

> 当有该镜像创建的容器存在时，镜像是无法删除的。如果要强行删除的话可以使用`docker rmi -f ID`

## 创建镜像

创建镜像的方法主要有三种：
- 基于已有镜像的容器创建
- 基于本地模板导入
- [基于Dockerfile创建](https://renguangli.com/docker-dockerfile)

### 基于已有镜像的容器创建

该方法主要是用`docker commit`命令创建镜像，主要参数为

- -a，--auther="" 作者信息
- -m, --message="" 提交信息

首先我们启动一个nginx镜像,然后进入容器，创建一个test文件，退出。

```
[root@localhost ~]# docker run -d -p 80:80 nginx
00fb093d0ec2044f6a5d62fe2aa64c487e427456bd53cb6dc8b6462be4e25167
[root@localhost ~]# docker exec -it 00fb093d0 bash 
root@00fb093d0ec2:/# touch test
root@00fb093d0ec2:/# exit
[root@localhost ~]#
```

这时容器已发生了改变，使用`docker commit`命令创建一个新的镜像

```
[root@localhost ~]# docker commit -a rgl -m "add a new file"    00fb093d0ec2  test:0.1
sha256:bcaa64525c2bde4e1329f7b28bc2c98b2947f83a5cb0efff6e9d968a85618930
```

创建成功的话会返回新镜像的id。

## 导出和导入镜像

如果要存出镜像到本地文件，可以使用`docker save`命令

```
[root@localhost ~]# docker save -o nginx.tar nginx:latest
```

这样就可以通过复制该镜像文件分享给其他人

如果要把镜像文件载入到本地镜像库，使用命令`docker load`。

```
[root@localhost ~]# docker load --input nginx.tar 
Loaded image: nginx:latest
```

或者

```
[root@localhost ~]# docker load < nginx.tar 
Loaded image: nginx:latest
```

## 上传镜像

使用`docker push`命令上传镜像到仓库，默认上传到Docker官方仓库（需要登录）。例如我们自制的nginx：1.10.0镜像上传，首先我们要添加新的标签user/nginx:1.10.0 ,然后用`docker push`上传镜像，user改成你的用户名。

```
[root@localhost ~]# docker push user/nginx:1.10.0
The push refers to a repository [docker.io/user/nginx]
110566462efa: Mounted from library/nginx 
305e2b6ef454: Mounted from library/nginx 
1.10.0: digest: sha256:d8565c25b654da69bc9b837a0dee713c988f0276e90564aa8fd12ebf4c2ff11e size: 948
```

## 参考资料

Docker技术入门与实战

Docker中文社区: <http://www.docker.org.cn/>

