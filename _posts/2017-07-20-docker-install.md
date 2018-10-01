---
layout: post
title: Docker学习笔记 - centos下安装Docker
category: Docker
tags: [Docker]
---

本文只演示centos下的Docker安装。其他系统请参考[Docker官方文档](https://docs.docker.com/install/linux/docker-ce/centos/#set-up-the-repository)

## Centos下安装docker

系统要求：64为操作系统，内核版本至少为3.10  
Docker目前支持Centos6.5及以上的版本

### yum安装

执行以下命令添加docker的yum源

```
yum-config-manager --add-repo    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 
```

对于centos7，Centos-Extras源中已经内置了docker，可以直接通过yum安装

``` bash
yum install -y docker
```

### 启动docker

```
systemctl start docker.service
```

### 设置docker开启自启动

```
systemclt enable docker.service
```
## 参考资料

Docker中文社区: <http://www.docker.org.cn/>

Docker技术入门与实战