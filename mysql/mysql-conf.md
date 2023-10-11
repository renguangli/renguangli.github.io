## 基础配置

```
[client]
default-character-set=utf8
port=3306
socket=/data/mysql/mysql.sock

[mysqld]
## 数据目录
datadir=/data/mysql/data
socket=/data/mysql/mysql.sock
## 进程id文件
pid-file=/data/mysql/data/mysqld.pid
## 错误日志
log-error=/var/log/mysqld.log
## 字符集
character-set-server=utf8mb4
default_collation_for_utf8mb4=utf8mb4_general_ci

general_log=ON
general_log_file=/data/mysql/data/general.log
```



## 慢查询日志

```
## 是否开启慢日志，0关闭，1开启
slow_query_log=1
## 查询时常超过多少时间记录，单位秒
long_query_time=3

## 是否记录不使用用索引的sql，0不记录，1记录，默认不记录
# log_queries_not_using_indexes=1
## 慢日志保存方式， FILE,table  (保存在 mysql.show_log 表中)
log_output=FILE
## 慢查询日志文件
slow_query_log_file=/data/mysql/data/slow.log
```

## 大小写

```
## 表名是否忽略大小写，1 
lower_case_table_names=1
```

## 隔离级别

```
transaction_isolation=READ-COMMITTED
```

