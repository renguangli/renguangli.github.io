---
layout: post
title: 使用 Dockerfile 创建 Docker 镜像
category: Docker
tags: [Docker]
---

以下是Dockerfile常用指令的简单介绍。

## Dockerfile常用指令

### FROM

`FROM`指定基础镜像，若镜像不存在Docker会从docker hub中 来查找该镜像。`FROM`命令必须是Dockerfile的首个命令。

```
FROM centos:7.2.1511
```

### MAINTAINER

维护者信息,一般用来指定作者,紧跟`FROM`命令

```
MAINTAINER Ren Guangli <renguangli@bonc.com.cn>
```

### ADD

Dockerfile中的`COPY`指令和`ADD`指令都可以将主机上的资源复制或加入到容器镜像中，都是在构建镜像的过程中完成的。`ADD`指令不仅能够将构建命令所在的主机本地的文件或目录，而且能够将远程URL所对应的文件或目录，作为资源复制到镜像文件系统。如果源文件是identity, gzip, bzip2，xz，tar.gz，tgz等类型的压缩文件，会添加tar -x命令，自动解压

> 对于从远程URL获取资源的情况，由于ADD指令不支持认证，如果从远程获取资源需要认证，则只能使用RUN wget或RUN curl替代。
另外，如果源路径的资源发生变化，则该ADD指令将使Docker Cache失效，Dockerfile中后续的所有指令都不能使用缓存。因此尽量将ADD指令放在Dockerfile的后面。

```
# exec格式用法
ADD ["<src>",... "<dest>"]
ADD ["hello.sh","docker.sh","/mnt"]
```

适合路径中带有空格的情况

```
# shell格式用法
ADD <src>... <dest>
ADD hello.sh docker.sh /mnt
```

注意事项
- 源路径可以有多个
- 源路径是相对于执行build的相对路径
- 源路径如果是本地路径，必须是build上下文中的路径
- 源路径如果是一个目录，则该目录下的所有内容都将被加入到容器，但是该目录本身不会
- 目标路径必须是绝对路径，或相对于WORKDIR的相对路径
- 目标路径如果不存在，则会创建相应的完整路径
- 路径中可以使用通配符

### COPY

和`ADD`指令类似，只是不能从远端获取资源和自动解压压缩文件

### ENV

配置环境变量

```
ENV <key> <value>
ENV <key>=<value> ...
```

第二种格式可以设置多个键值对，推荐在一条`ENV`指令中设置多个键值对，因为这样产生一个缓存层。

```
ENV JAVA_HOME=/mnt/jdk1.7 PATH=$JAVA_HOME/bin:$PATH CLASSPATH=.:$JAVA_HOME/lib
```

### EXPOSE

```
EXPOSE <port> [<port>...]
```

指定于外界交互的端口

```
EXPOSE 8080 443
```

在容器启动时用-p传递参数，例如`docker run -d -p 8088:8080 -p 8089:443 tomcat:7.0.81`将容器内的8080绑定到本机的8088端口,443绑定到本机的8089端口

### VOLUME

```
VOLUME ["/mnt"] 
```

创建一个可以从本地主机或其他容器挂载的挂载点，一般用来存放数据库和需要保持的数据等

### USER

```
USER root
```

指定运行容器时的用户名或 UID，后续的RUN、CMD、ENTRYPOINT也会使用指定用户。

### WORKDIR

```
WORKDIR /path/to/workdir
```

为后续的RUN、CMD、ENTRYPOINT指令配置工作目录。可以使用多个WORKDIR指令，后续命令如果参数是相对路径，则会基于之前命令指定的路径

```
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```

最终路径是/a/b/c。

WORKDIR指令可以在ENV设置变量之后调用环境变量:

```
ENV DIRPATH /path
WORKDIR $DIRPATH/$DIRNAME
```

最终路径则为 `/path/$DIRNAME`。

### RUN

```
RUN <command> 或 RUN ["", "", ""]
```

每条指令将在当前镜像基础上执行，并提交为新的镜像。（可以用“\”换行）

### CMD

```
CMD ["","",""]
```

指定启动容器时执行的命令，每个Dockerfile只能有一条CMD指令，如果指定了多条指令，则最后一条执行。（会被`docker run`提供的参数覆盖）

### ENTRYPOINT

```
ENTRYPOINT ["","",""]
```

配置容器启动后执行的命令，并且不可被`docker run`提供的参数覆盖。（每个 Dockerfile 中只能有一个 ENTRYPOINT ，当指定多个时，只有最后一个起效）

### ONBUILD

```
ONBUILD [INSTRUCTION]
```

配置当所创建的镜像作为其它新创建镜像的基础镜像时，所执行的操作指令

## 使用Dockerfile创建镜像

我们使用Dockerfile创建一个springboot(app.jar)项目的镜像。

创建Dockerfile，内容如下

```
# 指定基础镜像
FROM centos:7.2.1511 

# 维护者信息
MAINTAINER renguangli <renguangligg@gmail.com>

# 复制jdk、app.jar到镜像里
ADD jdk-7u80-linux-x64.tar.gz /
COPY app.jar /

# 配置环境变量
ENV JAVA_HOME=/jdk1.7.0_80
ENV PATH=$JAVA_HOME/bin:$PATH
ENV CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# 指定与外界交互端口
EXPOSE 8080

# 启动命令
CMD ["java","-jar","/app.jar"]
```

### 创建镜像

将jdk-7u80-linux-x64.tar.gz和app.jar复制到Dockerfile所在文件夹下

执行构建镜像命令

```
[root@localhost data]# docker build -t renguangli/springboot .
Sending build context to Docker daemon 363.6 MB
Step 1/9 : FROM centos:7.2.1511
 ---> 0a2bad7da9b5
Step 2/9 : MAINTAINER renguangli <renguangligg@gmail.com>
 ---> Using cache
 ---> 124d16cddae8
Step 3/9 : ADD jdk-7u80-linux-x64.tar.gz /
 ---> a4e173d7fc26
Removing intermediate container 7ec1e0544288
Step 4/9 : COPY app.jar /
 ---> 1a4ec89553bd
Removing intermediate container 8483119a6bd1
Step 5/9 : ENV JAVA_HOME /jdk1.7.0_80
 ---> Running in 7c26a419fc30
 ---> 9cb0eb20d5cc
Removing intermediate container 7c26a419fc30
Step 6/9 : ENV PATH $JAVA_HOME/bin:$PATH
 ---> Running in 103dd1cd4fe3
 ---> 30965ea14640
Removing intermediate container 103dd1cd4fe3
Step 7/9 : ENV CLASSPATH .:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
 ---> Running in 47b8ee5519b3
 ---> 8f8ace07c09e
Removing intermediate container 47b8ee5519b3
Step 8/9 : EXPOSE 8080
 ---> Running in cf83f38ac195
 ---> 462090458def
Removing intermediate container cf83f38ac195
Step 9/9 : CMD java -jar /app.jar
 ---> Running in c00f819e946b
 ---> f79fd9a24a50
Removing intermediate container c00f819e946b
Successfully built f79fd9a24a50
```

### 查看生成的镜像 

``` bash
[root@localhost data]# docker images |grep springboot
springboot                  latest              f79fd9a24a50        About a minute ago   516 MB
```
### 创建容器并运行

``` bash
[root@localhost data]# docker run -d -p 8080:8080 springboot
```

### 访问

![](/images/docker/docker-app.png)

## 参考资料

Docker技术入门与实战

Docker中文社区: <http://www.docker.org.cn/>

<http://www.docker.org.cn/dockerppt/114.html>    

<http://www.cnblogs.com/sorex/p/6481407.html>  

<http://blog.csdn.net/taiyangdao/article/details/73222601> 