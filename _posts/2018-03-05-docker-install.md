---
layout: post
title: Docker在centos下的安装
category: Docker
tags: [Docker]
---

本文只演示centos下的Docker安装。其他系统请参考[Docker官方文档](https://docs.docker.com/install/linux/docker-ce/centos/#set-up-the-repository)

## centos下安装docker

系统要求：64为操作系统，内核版本至少为3.10  
Docker目前支持Centos6.5及以上的版本

### 安装

执行以下命令添加Docker的yum源

```
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 
```

对于centos7，Centos-Extras源中已经内置了Docker，可以直接通过yum安装

``` bash
yum install -y docker
```

### 启动Docker

```
systemctl start docker.service
```

### 设置Docker开启自启动

```
systemclt enable docker.service
```

## 配置阿里云镜像

官方镜像仓库下载速度比较慢，配置国内镜像仓库来加速镜像下载。

### 获取阿里云镜像加速地址

打开阿里云控制台，选择容器镜像服务，镜像加速器，复制加速器地址

![](/images/docker/docker-aliyun-registry.png)

### 修改配置文件

vi /etc/docker/daemon.json

``` bash
{
    "registry-mirrors":["https:sdf3ldsf.aliyuncs.com"]
} 
```

或者使用中国科学技术大学镜像地址：https://docker.mirrors.ustc.edu.cn

重启Docker，然后pull镜像就会发现速度大大增加了

## 参考资料

Docker中文社区: <http://www.docker.org.cn/>

Docker技术入门与实战