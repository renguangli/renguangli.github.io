下载地址：https://dev.mysql.com/downloads/mysql/

点击直接下载 64 位 5.7.44 版本的安装包 [mysql-5.7.44-linux-glibc2.12-x86_64.tar.gz](https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.44-linux-glibc2.12-x86_64.tar.gz)

### 1、创建用户、用户组

```
[root@localhost local]# groupadd mysql
[root@localhost local]# useradd -g mysql -s /sbin/nologin mysql
```

### 2、将二进制包上传至`/usr/local`目录解压并重命名为 mysql

```
[root@localhost local]# pwd
/usr/local
[root@localhost local]# tar -zxvf mysql-5.7.44-linux-glibc2.12-x86_64.tar.gz
[root@localhost local]# mv mysql-5.7.44-linux-glibc2.12-x86_64 mysql
```

### 3、配置环境变量

```
[root@localhost local]# cd mysql/bin/
[root@localhost bin]#  pwd
/usr/local/mysql/bin
[root@localhost bin]# echo "export PATH=$PATH:/usr/local/mysql/bin" >> /etc/profile
[root@localhost bin]# source /etc/profile
```

### 4、创建数据目录并赋权

```
[root@localhost mysql]# pwd
/data/mysql
[root@localhost mysql]# mkdir data
[root@localhost mysql]# chown -R mysql.mysql data
```

### 5、编辑配置文件

```
[root@localhost mysql]# vi /etc/my.cnf
[mysqld]
server-id = 1
# 端口
port=3306
# 数据目录
basedir=/usr/local/mysql
datadir=/data/mysql/data
socket=/data/mysql/logs/mysql.sock
# 进程id文件
pid-file=/data/mysql/logs/mysqld.pid
#错误日志
log-error=/data/mysql/logs/mysqld.log
# bin-log
log-bin=mysql-bin
# binlog_format=mixed 过时，
# expire_logs_days=30 过时，binlog_expire_logs_seconds
binlog_expire_logs_seconds=2592000
# 字符集
character-set-server=utf8mb4
# 大小写敏感：0敏感，1不敏感，默认为 0
lower_case_table_names=1
# 字符集
character-set-server=utf8mb4
# 最大连接数
max_connections=1000

# 是否开启慢日志，0关闭，1开启
slow_query_log=1
# 查询时常超过多少时间记录，单位秒
long_query_time=3
# 是否记录不使用索引的sql，0不记录，1记录，默认不记录
#log_queries_not_using_indexes=1
# 慢日志保存方式， FILE,table  (保存在 mysql.show_log 表中)
log_output=FILE
# 慢查询日志文件
slow_query_log_file=/data/mysql/logs/slow.log

[client]
socket=/data/mysql/logs/mysql.sock
```

### 6、初始化 MySQL

```
[root@localhost mysql]# mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql/data 
2018-11-07T21:50:50.815257Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-11-07T21:50:51.175245Z 0 [Warning] InnoDB: New log files created, LSN=45790
2018-11-07T21:50:51.263531Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-11-07T21:50:51.470037Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 31b6a6d5-e2d7-11e8-9ee8-000c2970d10a.
2018-11-07T21:50:51.496683Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-11-07T21:50:51.497829Z 1 [Note] A temporary password is generated for root@localhost: QHSpB01maf.!
```

记住最后一行生成临时 root 密码 : QHSpB01maf.!

### 7、启动

```
[root@localhost mysql]# cp support-files/mysql.server /etc/init.d/mysqld
[root@localhost mysql]# service mysqld start
Starting MySQL. SUCCESS!
[root@localhost mysql]# mysql -u root -pQHSpB01maf.!
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.44

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use mysql；
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
### 首次登陆需要修改root用户密码
mysql> alter user 'root'@'localhost' identified by 'root';
### 或者
mysql> alter user user() identified by 'root';
Query OK, 0 rows affected (0.00 sec)
```

### 8、远程连接配置

```
mysql> grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

```
FLUSH PRIVILEGES;
```
