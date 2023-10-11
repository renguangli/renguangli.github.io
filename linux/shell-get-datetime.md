1. 获取当前时间（原格式输出）

```
[renguangli@localhost ~]$ date
Sat May 29 15:23:51 CST 2021
```

2. 输出指定格式（yyyy-MM-dd HH:mm:ss）

```
[renguangli@localhost ~]$ date "+%Y-%m-%-d %H:%M:%S"
2021-05-29 15:25:56
```

3. 参数解释

```
1 Y显示4位年份，如：2021；y显示2位年份，如：21。
2 m表示月份；M表示分钟。
3 d表示天；D则表示当前日期，如：1/18/18(也就是2018.1.18)。
4 H表示小时，而h显示月份。
5 s显示当前秒钟，单位为毫秒；S显示当前秒钟，单位为秒。
```

4. 获取一天前的时间

```
[renguangli@localhost ~]$ date -d "1 days ago"   "+%Y-%m-%-d %H:%M:%S"
2021-05-28 15:30:59
```

5. 获取一小时前的时间

```
[renguangli@localhost ~]$ date -d "1 hours ago"   "+%Y-%m-%-d %H:%M:%S"
2021-05-29 14:31:48
```

