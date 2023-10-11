1. 主库配置

主库添加主从同步用户

```mysql
create user slave identified with mysql_native_password by 'Slave@123'
GRANT REPLICATION SLAVE ON *.* to 'slave'@'24.43.158.52' identified by 'Slave@123';
FLUSH PRIVILEGES;
```

修改主库配置文件

```
vim /etc/my.cnf
server-id=1
log-bin=/data/mysql/binlog/bin-log
#指定同步的数据库，如果不写，默认是同步所有数据库
#binlog-do-db = 数据库
#指定不同步的数据库　　
binlog-ignore-db=mysql,information_schema,performance_schema
#binlog日志的保留时间
expire_logs_days=7
#指定binlog日志的格式mixed/row
binlog_format=MIXED
```

重启主库

```
service mysqld restart
```

查看主服务器当前二进制日志名和偏移量

```
mysql> show master status;

+---------------+----------+--------------+------------------+
| File          | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+---------------+----------+--------------+------------------+
| bin-log.000001 |     21526 | 数据库（空为所有）        |                  |
+---------------+----------+--------------+------------------+
1 row in set (0.00 sec)
```

到此主服务器已经配置好：

2. 从库配置

修改配置文件

```
vim /etc/my.cnf
#master端的id号，不唯一即可
server-id=2
#同步的日志路径及文件名，一定注意这个目录要是mysql有权限写入的
log-bin=/data/mysql/binlog/bin-log
#不复制哪个库
replicate-ignore-db=mysql,information_schema,performance_schema
#这两个是启用relaylog的自动修复功能，避免由于网络之类的外因造成日志损坏，主从停止，保证数据写入的一致性
master_info_repository=table
relay_log_info_repository=table
```

重启服务

```
service mysqld restart
```

配置主从同步

```mysql
CHANGE MASTER TO MASTER_HOST='24.43.158.51',
MASTER_PORT=3306,
MASTER_USER='slave',
MASTER_PASSWORD='Slave@123',
MASTER_LOG_FILE='bin-log.000001',
MASTER_LOG_POS=21526; 
```

启动主从同步

```
start slave
```

查看同步状态

```
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```

两个参数为yes时成功