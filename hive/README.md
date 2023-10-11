### 恢复分区

```sql
msck repair table talbe_name
```



### 创建表

```
create table if not exists tab_name{
	time string comment '时间',
	code string comment '点码',
	value doulbe comment '值'
} partitioned by(
	year string comment '年',
	month string comment '月',
	day string comment '日',
	hour string comment '小时'
)
row format delimited fields terminated by ','
```

### 添加删除分区

添加分区

```
alter table tab_name add partition (year='2021',month='06',day='04',hour='01')
```

删除分区

```
alter table tab_name drop partition(year='2021',month='06',day='04',hour='01')
```

### 向分区表中插入数据

表结构

time,code,value (test)

time,code,value,dt (test_p)

指定分区插入数据

```
insert overwrite test_p partition(dt='20210601')
select * from test where time > '2021-06-01' and time < '2021-06-02'
```

动态分区插入

```
insert overwrite test_p partition(dt)
select time,code,value,from_unixtime(unix_timestamp(time,'yyyy-MM-dd HH:mm:ss'))
```

