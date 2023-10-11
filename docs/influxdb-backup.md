## influxdb backup 备份两个月数据

开始时间：2022/09/16 11:05:32

![image-20220916114443307](/Users/renguangli/docs/notes/docs/img/image-20220916114443307.png)

结束时间：2022/09/16 11:36:43

![image-20220916114412846](/Users/renguangli/docs/notes/docs/img/image-20220916114412846.png)



## influxd restore 恢复到 influxdb

开始时间：2022/09/16 12:17:33 

结束时间：2022/09/16 13:14:31

## influxdb 转存 iotdb 日志查看

部署了5个实例

```
24.43.102.225
/data/0-15000
/data/15000-30000
/data/30000-45000
```

```
24.43.102.232
/data/45000-60000
/data/60000-72329
```

查看日志

```
cd /data/0-15000
tail -f logs/datac.2022-09-16.0.log
## 日志文件名为当天日期
```

![image-20220916161256770](/Users/renguangli/docs/notes/docs/img/image-20220916161256770.png)

当看到influxdb[0,15000] 数据写入 iotdb 完成！