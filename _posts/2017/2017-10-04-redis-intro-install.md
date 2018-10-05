---
layout: post
title: Redis学习笔记 - 简介与安装
category: 设计模式
tags: [设计模式]
---

Redis是一种基于键值对的内存数据库数据库，由string(字符串)、hash(哈希)、list(列表)、set(集合)、zset(有序集合)、BitMaps(位图)、HyperLogLog、GEO(地理信息定位)等多种数据结构和算法组成。

## Redis特性

### 高性能

Redis官网给出的读写性能是10万每秒，有兴趣的可以参考官方的基准程序测试《How fast is Redis？》（https://redis.io/topics/benchmarks）

Redis速度快的主要原因可以归纳为一下4点
- Redis所有数据存储与内存中，内存的读写速度大概是100ns
- Redis是C语言实现的，更接近操作系统
- Redis使用了单线程架构，避免了多线程竞争问题
- Redis的源代码是作者精心设计的

### 多种数据结构

Redis的全称是Remote Dictionary Server,主要提过了5中数据结构：字符床、哈希、列表、集合、有序集合，同时在字符串的基础上提供了位图和HyperLogLog两种数据机构。

Redis3.2版本中加入了有关地理信息定位的数据结构GEO。

### 功能丰富

Redis除了5中基本数据机构外，还提供了其他丰富的功能
- 提供了键过期功能，可以用来实现缓存
- 提供了发布订阅功能，可以用来实现消息系统
- 支持Lua脚本，可以利用Lua创造出新的Redis命令
- 提供了简单的事务功能，能在一定程度上保证事务特性
- 提供了流行线(Pipeline)功能，这样客户端能叫一批命令一次性传到Redis，减少网路开销

### 持久化

Redis提供了RDB和AOF两种持久化方式。

### 主从复制

Redis提供了复制功能，实现了多个相同数据的Redis副本

### 高可用和分布式

Redis2.8版本提供了高可用实现，它能够保证Redis节点的故障节点的发现和故障自动转移。

Redis从3.0版本开始提供了分布式实现Redis Cluster,它是Redis真正的分布式实现，提供了高可用、读写和容量的扩展性

## Redis使用场景

- 缓存
- 排行榜系统
- 计数器应用
- 社交网路
- 消息队列系统

## 安装

### 在window下安装Redis

window版本Redis下载地址：<https://github.com/MicrosoftArchive/redis/releases>

我下载的是解压版本，下载完成后，将其解压到C:\soft\Redis-x64-3.2.100目录(随便哪里都可以)

![](/images/redis/redis-win-dir.jpg)

双击redis-server.exe启动redis

![](/images/redis/redis-win-server.jpg)

双击redis-cli.ext启动redis客户端

![](/images/redis/redis-win-cli.jpg)

### 在linux下安装Redis

