1.点码（excel)

2.点码规范(图片)

3.

数据备份与恢复

官网文档地址：https://docs.influxdata.com/influxdb/v1.7/administration/backup_and_restore/

## 备份

使用 influxd backup 命令备份，命令格式如下

```
influxd backup
    [ -database <db_name> ]
    [ -portable ]
    [ -host <host:port> ]
    [ -retention <rp_name> ] | [ -shard <shard_ID> -retention <rp_name> ]
    [ -start <timestamp> [ -end <timestamp> ] | -since <timestamp> ]
    <path-to-backup>
```

**Examples**

备份所有数据库

```
influxd backup -portable /tmp/backup
```

备份所有数据库时间 2021-05-01T00:00:00Z 以后的数据

```
influxd backup -portable -start 2021-05-01T00:00:00Z /tmp/backup
```

备份指定数据库：telegraf

```
influxd backup -portable -database telegraf /tmp/backup
```

备份指定数据库telegraf时间从 2021-05-01T00:00:00Z 到 2021-06-01T00:00:00Z  之间的数据

```
influxd backup  -portable -database telegraf -start 2021-05-01T00:00:00Z  -end 2021-06-01T00:00:00Z 
```

## 恢复

使用 influxd restore 命令恢复数据，命令格式如下

```
influxd restore [ -db <db_name> ]
    -portable | -online
    [ -host <host:port> ]
    [ -newdb <newdb_name> ]
    [ -rp <rp_name> ]
    [ -newrp <newrp_name> ]
    [ -shard <shard_ID> ]
    <path-to-backup-files>
```

**Examples**

恢复所有数据库数据

```
influxd restore -portable path-to-backup
```

恢复指定数据库 telegraf 的数据

```
influxd restore -portable -db telegraf path-to-backup
```

### 恢复到已存在的库

```
influxd restore -portable -db telegraf -newdb telegraf_bak path-to-backup
```

```
> USE telegraf_bak
> SELECT * INTO telegraf..:MEASUREMENT FROM /.*/ GROUP BY *
> DROP DATABASE telegraf_bak
```

