使用 java 客户端写入 hdfs 时报如下异常

```java
WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform… 
using builtin-java classes where applicable
```

进入 hadoop 安装目录，将 hadoop/lib/native 目录中的文件拷贝到客户端服务器上

启动时加入参数 -Djava.library.pat=./lib/native

```java
java  -Djava.library.path=./lib/native -jar xx.jar
```

