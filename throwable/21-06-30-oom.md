一个简单的后台接口应用经常有规律性的挂掉

简单看了下日志，主要是因为内存溢出了，具体原因从日志里没看出什么

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## 排查过程

1. 添加 jvm 参数

```
@echo off
set "JAVA_OPTS=-Xms2048m -Xmx2048m"
set "JAVA_OPTS=%JAVA_OPTS% -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=D:/jiu_screen_logs/java.hprof"
set "JAVA_OPTS=%JAVA_OPTS% -XX:+PrintGCDetails -XX:+PrintGCDateStamps"
set "JAVA_OPTS=%JAVA_OPTS% -Xloggc:D:/jiu_screen_logs/gc-%t.log -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=14 -XX:GCLogFileSize=100M"
START javaw %JAVA_OPTS% -jar D:\jiu-0.0.1-SNAPSHOT.jar
exit
```

- -Xms2048m -Xmx2048m : 堆内存最小最大值
- -XX:+HeapDumpOnOutOfMemoryError： 发生 OOM 时，自动生成 DUMP 文件
- -XX:HeapDumpPath=D:/jiu_screen_logs/java.hprof ： DUMP 文件路径
- -XX:+PrintGCDetails：打印 GC 详情
- -XX:+PrintGCDateStamps：打印 GC 时间戳
- -Xloggc:D:/jiu_screen_logs/gc-%t.log ：GC 日志路径
- -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=14 -XX:GCLogFileSize=100M ： GC 日志滚动策略，最多14文件，文件大小最大 100 M

主要添加了 gc 相关的参数，以及发生 OOM 时生成 dump 文件

2. 应用第二次挂掉后就会生成 DUMP 文件，使用 JvisualVm 分析 DUMP 文件

   ![image-20210630180113253](image-20210630180113253.png)

   发现对象最多的的 String 类，点进去查看详情，看看值都是写什么

   ![image-20210630180242912](image-20210630180242912.png)

   从图中可以看出生成了好多时间序列，可能是由于死循环导致

3. 在源码中搜索 ‘HH:mm’找到有一处，生成时间序列的方法

   ```java
   public static void generateDayTimeStr(String startTime, String endTime, List<String> xData) {
       DateTimeFormatter df = DateTimeFormatter.ofPattern("HH:mm");
       LocalTime sl = LocalTime.parse(startTime,df);
       LocalTime el = LocalTime.parse(endTime,df);
       while (sl.isBefore(el)){
           xData.add(sl.toString());
           sl = sl.plusHours(1);
       }
   }
   ```

   这段代码正常情况下是没有问题的，但是如果 endTime 是 23 点多的话就有问题了

    s1 每次加 1 小时，当 s1 加到 23:00 时，此时 sl 小于 el，循环继续

   当 s1 再加 1 小时 s1 就变成 00:00，这样就导致循环无法结束了

   根本原因是溢出了，只有时间，没有日期

4. 解决方案：使用 LocalDatetime 就不会有这样的问题了

   ```
   public static void generateDayTimeStr(LocalDateTime startTime, LocalDateTime endTime) {
       while (startTime.isBefore(endTime)){
       	xData.add(startTime.format(DateTimeFormatter.ofPattern("HH:mm")));
       	startTime = startTime.plusHours(1);
       }
   }
   ```