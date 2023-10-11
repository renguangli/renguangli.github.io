## 备份示例

1. 备份全部数据库的数据和结构

```
mysqldump -uroot -p123456 -A > /data/bak/mydb.sql
```

2. 备份全部数据库的结构（加 -d 参数）

```
mysqldump -uroot -p123456 -A -d > /data/bak/mydb.sql
```

3. 备份全部数据库的数据(加 -t 参数)

```
mysqldump -uroot -p123456 -A -t > /data/mysqlDump/mydb.sql
```

4. 备份单个数据库的数据和结构(,数据库名mydb)

```
mysqldump -uroot-p123456 mydb > /data/bak/mydb.sql
```

5. 备份单个数据库的结构

```
mysqldump -uroot -p123456 mydb -d > /data/bak/mydb.sql
```

6. 备份单个数据库的数据

```
mysqldump -uroot -p123456 mydb -t > /data/bak/mydb.sql
```

7. 备份多个表的数据和结构（数据，结构的单独备份方法与上同）

```
mysqldump -uroot -p123456 mydb t1 t2 > /data/bak/mydb.sql
```

8. 一次备份多个数据库

```
mysqldump -uroot -p123456 --databases db1 db2 > /data/bak/mydb.sql
```

9. 条件备份

```
mysqldump -uroot -p123456 mydb t1 t2 --where="date=2021" > /data/bak/mydb.sql
```

## 还原示例

有两种方式还原，第一种是在 MySQL 命令行中，第二种是使用 SHELL 行完成还原

1. 在系统命令行中，输入如下实现还原：

```
mysql -uroot -p123456 < /data/bak/mydb.sql
```

2. 在登录进入mysql系统中,通过source指令找到对应系统中的文件进行还原：

```
mysql> source /data/bak/mydb.sql
```

3. 恢复到指定苦

```
mysql -uroot -p123456 mydb < /data/bak/mydb.sql
```

