```
## 必备
-XX:+PrintGCDetails
-XX:+PrintGCDateStamps
-XX:+PrintTenuringDistribution
-XX:+PrintHeapAtGC
-XX:+PrintReferenceGC
-XX:+PrintGCApplicationStoppedTime

## 可选
-XX:+PrintSafepointStatistics
-XX:PrintSafepointStatisticsCount=1
-XX:+HeapDumpOnOutOfMemoryError：当发生OOM错误 时，将内存DUMP下来
-XX:HeapDumpPath=/home/admin/logs/java.hprof：设置内存DUMP的路径
-XX:+HeapDumpBeforeFullGC
-XX:+HeapDumpAfterFullGC：在Full GC前后分别对内存做一个dump

## GC日志输出的文件路径
-Xloggc:/path/to/gc-%t.log
## 开启日志文件分割
-XX:+UseGCLogFileRotation
## 最多分割几个文件，超过之后从头文件开始写
-XX:NumberOfGCLogFiles=14
## 每个文件上限大小，超过就触发分割
-XX:GCLogFileSize=100M
```

