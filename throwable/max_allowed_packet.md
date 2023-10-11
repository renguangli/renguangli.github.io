>  max_allowed_packet 是 MySQl 的一个常见参数，mysql 根据max_allowed_packet大小 限制 server 接受的数据包大小。

用 Navicat 导入 SQL 文件时报错：

```
[ERR] 1153 - Got a packet bigger than 'max_allowed_packet' bytes
```

MySQL 默认读取执行的 SQL 文件最大为16M，sql脚本实际 260M

> 解决方法

修改配置文件 my.cnf ，将默认值调大

```
vi /etc/my.cnf
[mysqld]
max_allowed_packet=400M
```

重启 MySQL

```
service restart mysqld
```

