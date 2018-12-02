---
layout: post
title: MySQL 在 Linux 和 Windows 下的安装与配置
category: [MySQL]
tags: [MySQL]
---

### Linux下 MySQL 二进制安装

1、创建用户、用户组

[root@localhost local]# groupadd mysql
[root@localhost local]# useradd -g mysql -s /sbin/nologin mysql

2、将二进制包解压到指定目录并重命名为mysql

[root@localhost local]# pwd
/usr/local
[root@localhost local]# tar -zxvf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
[root@localhost local]# mv mysql-5.7.24-linux-glibc2.12-x86_64 mysql
[root@localhost local]# ll
总用量 629816
drwxr-xr-x. 2 root root         6 4月  11 2018 bin
drwxr-xr-x. 2 root root         6 4月  11 2018 etc
drwxr-xr-x. 2 root root         6 4月  11 2018 games
drwxr-xr-x. 2 root root         6 4月  11 2018 include
drwxr-xr-x. 2 root root         6 4月  11 2018 lib
drwxr-xr-x. 2 root root         6 4月  11 2018 lib64
drwxr-xr-x. 2 root root         6 4月  11 2018 libexec
drwxr-xr-x. 9 root root       129 11月  8 05:26 mysql
-rw-r--r--. 1 root root 644930593 10月 29 11:44 mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
drwxr-xr-x. 2 root root         6 4月  11 2018 sbin
drwxr-xr-x. 5 root root        49 11月  4 08:42 share
drwxr-xr-x. 2 root root         6 11月  8 05:33 src

3、配置环境变量

[root@localhost local]# cd mysql/bin/
[root@localhost bin]# pwd
/usr/local/mysql/bin
[root@localhost bin]# echo "export PATH=$PATH:/usr/local/mysql/bin" >> /etc/profile
[root@localhost bin]# source /etc/profile

4、创建数据目录并赋权

[root@localhost mysql]# pwd
/usr/local/mysql
[root@localhost mysql]# mkdir data

5、编辑配置文件

[root@localhost mysql]# vi /etc/my.cnf
[client]
port=3306
default-character-set=utf8
socket=/usr/local/mysql/run/mysql.sock
[mysqld]
pid_file=/usr/local/mysql/run/mysql.pid
datadir=/usr/local/mysql/data
max_connections=200
character-set-server=utf8

6、初始化 MySQL

[root@localhost mysql]# mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
2018-11-07T21:50:50.815257Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-11-07T21:50:51.175245Z 0 [Warning] InnoDB: New log files created, LSN=45790
2018-11-07T21:50:51.263531Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-11-07T21:50:51.470037Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 31b6a6d5-e2d7-11e8-9ee8-000c2970d10a.
2018-11-07T21:50:51.496683Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-11-07T21:50:51.497829Z 1 [Note] A temporary password is generated for root@localhost: QHSpB01maf.!
记住最后一行生成临时root密码

7、编辑配置文件
[root@localhost data]# vi /etc/my.cnf

[mysqld]
port=3306
datadir=/usr/local/mysql/data
max_connections=200
character-set-server=utf8

8、启动
[root@localhost mysql]# cp support-files/mysql.server /etc/init.d/mysql
[root@localhost mysql]# service mysql start
Starting MySQL. SUCCESS!
[root@localhost mysql]# mysql -u root -pQHSpB01maf.!
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.24

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use mysql；
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
## 首次登陆需要修改root用户密码
mysql> alter user 'root'@'localhost' identified by 'root';或者alter user user() identified by 'root';
Query OK, 0 rows affected (0.00 sec)

## 配置root用远程登陆且具有所有库的权限
mysql> grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;
Query OK, 0 rows affected, 1 warning (0.00 sec)


### Windows下 MySQL 解压版安装与配置 

从 MySQL 官网下载解压版本的安装包

下载地址：<https://dev.mysql.com/downloads/file/?id=481160>

点击直接下载：<https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.24-winx64.zip>

下载完成后解压到安装位置，我的安装位置为

```
D:\database\mysql-5.7.24
```

在 `D:\database\mysql-5.7.24` 目录下新建配置文件 `my.ini`

```
[mysql]
default-character-set=utf8
[mysqld]
port = 3306
basedir=D:\database\mysql-5.7.24
datadir=D:\database\mysql-5.7.24\data
max_connections=200
character-set-server=utf8
default-storage-engine=INNODB
```

5.7.24 版本安装包已不包含 my.default.ini 配置文件和 data 目录。

**data 目录不要新建**，my.ini 配置文件可以新建，data 目录通过初始化命令来创建。

以管理员的方式打开 cmd 命令窗口，进入 D:\database\mysql-5.7.24\bin 目录下执行以下命令，初始化数据库。

```
D:\database\mysql-5.7.24\bin>mysqld --initialize --console
2018-11-05T09:09:27.549968Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-11-05T09:09:29.253650Z 0 [Warning] InnoDB: New log files created, LSN=45790
2018-11-05T09:09:29.850310Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-11-05T09:09:30.180265Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 80b77eb4-e0da-11e8-ac91-5215d904abe2.
2018-11-05T09:09:30.225972Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-11-05T09:09:30.244958Z 1 [Note] A temporary password is generated for root@localhost: T%loP7wOUdOz

```

`mysqld --initialize` 会随机生成临时密码 `T%loP7wOUdOz`。

配置 MySQL 为Windows 服务
```
mysqld install 
```

管理员的身份运行 CMD，启动 MySQL
```
net start|stop mysql
```

```
[root@localhost mysql]# mysql -u root -pT%loP7wOUdOz
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.24

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use mysql；
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
## 首次登陆需要修改root用户密码
mysql> alter user 'root'@'localhost' identified by 'root';或者alter user user() identified by 'root';
Query OK, 0 rows affected (0.00 sec)

## 配置root用远程登陆且具有所有库的权限
mysql> grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;
Query OK, 0 rows affected, 1 warning (0.00 sec)
```
