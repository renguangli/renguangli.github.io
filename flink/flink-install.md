### Flink 架构

Flink系统架构中包含了两个角色，分别是JobManager和TaskManager，是一个典型的Master-Slave架构。JobManager相当于是Master，TaskManager相当于是Slave

![The processes involved in executing a Flink dataflow](https://ci.apache.org/projects/flink/flink-docs-release-1.9/fig/processes.svg)



**JobManager（JVM进程）作用**

JobManager负责整个集群的资源管理与任务管理，在一个集群中只能由一个正在工作（active）的JobManager，如果HA集群，那么其他JobManager一定是standby状态

（1）资源调度

- 集群启动，TaskManager会将当前节点的资源信息注册给JobManager，所有TaskManager全部注册完毕，集群启动成功，此时JobManager就掌握整个集群的资源情况

- client提交Application给JobManager，JobManager会根据集群中的资源情况，为当前的Application分配TaskSlot资源

（2）任务调度

- 根据各个TaskManager节点上的资源分发task到TaskSlot中运行

- Job执行过程中，JobManager会根据设置的触发策略触发checkpoint，通知TaskManager开始checkpoint

- 任务执行完毕，JobManager会将Job执行的信息反馈给client，并且释放TaskManager资源


**TaskManager（JVM进程）作用**

- 负责当前节点上的任务运行及当前节点上的资源管理，TaskManager资源通过TaskSlot进行了划分，每个TaskSlot代表的是一份固定资源。例如，具有三个 slots 的 TaskManager 会将其管理的内存资源分成三等份给每个 slot。 划分资源意味着 subtask 之间不会竞争内存资源，但是也意味着它们只拥有固定的资源。注意这里并没有 CPU 隔离，当前 slots 之间只是划分任务的内存资源

- 负责TaskManager之间的数据交换


**client 客户端**

负责将当前的任务提交给JobManager，提交任务的常用方式：命令提交、web页面提交。获取任务的执行信息

### Standalone 集群安装

Standalone是独立部署模式，它不依赖其他平台，不依赖任何的资源调度框架

Standalone集群是由JobManager、TaskManager两个JVM进程组成

#### 集群角色划分

|   node01   |   node02    |   node03    |   node04    |
| :--------: | :---------: | :---------: | :---------: |
| JobManager | TaskManager | TaskManager | TaskManager |

------

#### 安装步骤

1. 官网下载Flink安装包

   Apache Flink® 1.10.0 is our latest stable release.现在最稳定的是1.10.0，不建议采用这个版本，刚从1.9升级到1.10，会存在一些bug，不建议采用小版本号为0的安装包，所以我们建议使用1.9.2版本

   下载链接:https://mirrors.tuna.tsinghua.edu.cn/apache/flink/flink-1.9.2/flink-1.9.2-bin-scala_2.11.tgz

2. 安装包上传到node01节点

3. 解压、修改配置文件

   解压：tar -zxvf flink-1.9.2-bin-scala_2.11.tgz

   修改flink-conf.yaml配置文件

   ```
   jobmanager.rpc.address: node01 	JobManager地址
   jobmanager.rpc.port: 6123      	JobManagerRPC通信端口
   jobmanager.heap.size: 1024m   	JobManager所能使用的堆内存大小
   taskmanager.heap.size: 1024m  	TaskManager所能使用的堆内存大小
   taskmanager.numberOfTaskSlots: 2 TaskManager管理的TaskSlot个数，依据当前物理机的核心数来配置，一般预留出一部分核心（25%）给系统及其他进程使用，一个slot对应一个core。如果core支持超线程，那么slot个数*2
   rest.port: 8081					指定WebUI的访问端口
   ```

   修改 slaves 配置文件

   ```
   node02
   node03
   node04
   ```

4. 同步安装包到其他的节点

   同步到node02  scp -r flink-1.9.2 node02:`pwd`

   同步到node03  scp -r flink-1.9.2 node03:`pwd`

   同步到node04  scp -r flink-1.9.2 node04:`pwd`

5. node01配置环境变量

   ```
   vim ~/.bashrc
   export FLINK_HOME=/opt/software/flink/flink-1.9.2
   export PATH=$PATH:$FLINK_HOME/bin
   source ~/.bashrc
   ```

6. 启动 standalone 集群

   启动集群：start-cluster.sh

   关闭集群：stop-cluster.sh

7. 查看Flink Web UI页面

   http://node01:8081/ 可通过rest.port参数自定义端口

#### 提交 Job 到 standalone 集群

常用提交任务的方式有两种，分别是命令提交和Web页面提交	

1. **命令提交：**

   flink run -c com.msb.stream.WordCount StudyFlink-1.0-SNAPSHOT.jar

   -c 指定主类

   -d 独立运行、后台运行 

   -p 指定并行度

2. **Web 页面提交：**

   在Web中指定Jar包的位置、主类路径、并行数等

   web.submit.enable: true一定是true，否则不支持Web提交Application

   ![1586705887854](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1586705887854.png)

------

3. 启动scala-shell测试

   ```
   start-scala-shell.sh remote <hostname> <portnumber>
   ```


### Standalone HA 集群安装&测试 

JobManager协调每个flink任务部署,它负责调度和资源管理

默认情况下，每个flink集群只有一个JobManager，这将导致一个单点故障(SPOF single-point-of-failure)：如果JobManager挂了，则不能提交新的任务，并且运行中的程序也会失败。

使用JobManager HA，集群可以从JobManager故障中恢复，从而避免SPOF

Standalone模式（独立模式）下JobManager的高可用性的基本思想是，任何时候都有一个 Active JobManager ，并且多个Standby JobManagers 。 Standby JobManagers可以在Master JobManager 挂掉的情况下接管集群成为Master JobManager。 这样保证了没有单点故障，一旦某一个Standby JobManager接管集群，程序就可以继续运行。 Standby JobManager和Active JobManager实例之间没有明确区别。 每个JobManager可以成为Active或Standby节点

![img](https://ci.apache.org/projects/flink/flink-docs-release-1.9/fig/jobmanager_ha_overview.png)

如何单独启动JobManager  jobmanager.sh

如何单独启动TaskManager  taskmanager.sh

#### 集群角色划分

|             | node01 | node02 | node03 | node04 |
| :---------: | :----: | :----: | :----: | :----: |
| JobManager  |   √    |   √    |   ×    |   ×    |
| TaskManager |   ×    |   √    |   √    |   √    |

#### 安装步骤

1. 修改配置文件conf/flink-conf.yaml

   ```
   high-availability: zookeeper 
   high-availability.storageDir: hdfs://node01:9000/flink/ha/ 保存JobManager恢复所需要的所有元数据信息
   high-availability.zookeeper.quorum: node01:2181,node02:2181,node03:2181 zookeeper地址
   ```

2. 修改配置文件conf/masters

   ```
   node01:8081
   node02:8081
   ```

3. 同步文件到各个节点

4. 下载支持Hadoop插件并且拷贝到各个节点的安装包的lib目录下

   下载地址：https://repo.maven.apache.org/maven2/org/apache/flink/flink-shaded-hadoop-2-uber/2.6.5-10.0/flink-shaded-hadoop-2-uber-2.6.5-10.0.jar

- HA集群测试

  http://node01:8081/

  http://node02:8081/

  两个页面一模一样 存在bug



