---
layout: post
title: Docker 在Centos 环境下的安装
category: Docker
tags: [Docker]
---

本文只演示 Centos 下的 Docker 安装。其他系统请参考[Docker官方文档](https://docs.docker.com/install/linux/docker-ce/centos/#set-up-the-repository)

## Centos 下安装 docker

系统要求：64 为操作系统，内核版本至少为 3.10，Docker 目前支持 Centos6.5 及以上的版本

### yum 安装

添加 Docker 的 yum 源

```
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 
```

对于 Centos7，Centos-Extras 源中已经内置了 Docker，可以直接通过yum安装

``` bash
yum install -y docker
```

测试

``` bash
docker version
```
输入上述命令，返回 Docker 版本相关信息，说明 Docker 安装成功。

### 启动/重启/关闭 Docker 

```
systemctl start docker.service
systemctl restart docker.service
systemctl stop docker.service
```

### 设置 Docker 开启自启

```
systemctl enable docker.service
```

## 参考资料

Docker 技术入门与实战

Docker 中文社区: <http://www.docker.org.cn/>

