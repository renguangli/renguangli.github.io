### 什么是 bond

网卡bond是通过多张网卡绑定为一个逻辑网卡，实现本地网卡的冗余，带宽扩容和负载均衡，在生产场景中是一种常用的技术。Kernels 2.4.12及以后的版本均供bonding模块，以前的版本可以通过patch实现。可以通过以下命令确定内核是否支持 bonding

### bond 的模式

- mode=0（balance-rr）

表示负载分担round-robin，并且是轮询的方式比如第一个包走eth0，第二个包走eth1，直到数据包发送完毕。 优点：流量提高一倍。缺点：需要接入交换机做端口聚合，否则可能无法使用

- mode=1（active-backup）

表示主备模式，即同时只有1块网卡在工作。

优点：冗余性高。缺点：链路利用率低，两块网卡只有1块在工作

- mode=3(broadcast)(广播策略)

  表示所有包从所有网络接口发出，这个不均衡，只有冗余机制，但过于浪费资源。此模式适用于金融行业，因为他们需要高可靠性的网络，不允许出现任何问题。需要和交换机的聚合强制不协商方式配合。

  特点：在每个slave接口上传输每个数据包，此模式提供了容错能力

- mode=4(802.3ad)(IEEE 802.3ad 动态链接聚合)

  表示支持802.3ad协议，和交换机的聚合LACP方式配合（需要xmit_hash_policy）.标准要求所有设备在聚合操作时，要在同样的速率和双工模式，而且，和除了balance-rr模式外的其它bonding负载均衡模式一样，任何连接都不能使用多于一个接口的带宽。

  特点：创建一个聚合组，它们共享同样的速率和双工设定。根据802.3ad规范将多个slave工作在同一个激活的聚合体下。外出流量的slave选举是基于传输hash策略，该策略可以通过xmit_hash_policy选项从缺省的XOR策略改变到其他策略。需要注意的是，并不是所有的传输策略都是802.3ad适应的，尤其考虑到在802.3ad标准43.2.4章节提及的包乱序问题。不同的实现可能会有不同的适应性。

  必要条件：

​    条件1：ethtool支持获取每个slave的速率和双工设定

​    条件2：switch(交换机)支持IEEE802.3ad Dynamic link aggregation

​    条件3：大多数switch(交换机)需要经过特定配置才能支持802.3ad模式

- mode=5(balance-tlb)(适配器传输负载均衡)

是根据每个slave的负载情况选择slave进行发送，接收时使用当前轮到的slave。该模式要求slave接口的网络设备驱动有某种ethtool支持；而且ARP监控不可用。

特点：不需要任何特别的switch(交换机)支持的通道bonding。在每个slave上根据当前的负载（根据速度计算）分配外出流量。如果正在接受数据的slave出故障了，另一个slave接管失败的slave的MAC地址。

 必要条件：ethtool支持获取每个slave的速率

- mode=6(balance-alb)(适配器适应性负载均衡)

  在5的tlb基础上增加了rlb(接收负载均衡receiveload balance).不需要任何switch(交换机)的支持。接收负载均衡是通过ARP协商实现的.

> mode5和mode6不需要交换机端的设置，网卡能自动聚合。mode4需要支持802.3ad。mode0，mode2和mode3理论上需要静态聚合方式

一般使用 0 或 1 模式

### 配置 bond

现在有两个千兆网卡eno1和eno2进行bond 双网卡绑定

1. 关闭图形界面网络管理

```
systemctl stop NetworkManager
systemctl disable NetworkManager ## 关闭开机自启动
```

2. 配置物理网卡eno1和eno2

网卡eno1

```
cd /etc/sysconfig/network-scripts/
vi ifcfg-eno1
TYPE=Ethernet
BOOTPROTO=none
DEVICE=eno1
ONBOOT=yes
USERCLT=no
SLAVE=yes  ///可以没有此字段，就需要开机执行ifenslave bond0 eth0 eth1命令了。
MASTER=bond0
```

```
cd /etc/sysconfig/network-scripts/
vi ifcfg-eno2
TYPE=Ethernet
BOOTPROTO=none
DEVICE=eno2
ONBOOT=yes
USERCLT=no
SLAVE=yes
MASTER=bond0
```

3. 新建虚拟网卡 ifcfg-bond0

```
cd /etc/sysconfig/network-scripts/
vi ifcfg-bond0
TYPE=Ethernet
BOOTPROTO=static
NAME=bond0
DEVICE=bond0
ONBOOT=yes
IPADDR=10.119.4.14
NEMASK=255.255.255.0
GATEWAY=10.119.4.254
```

4. 配置 bonding

```
vi /etc/modprobe.d/bonding.conf //需要我们手工创建
alias bond0 bonding
options miimon=100 mode=0
```

5. 加载 bonding 模块

```
modprobe bonding
```

6. 重启网卡

```
systemctl restart network
```

可能会遇到ip冲突，或者网卡冲突的问题，根据实际情况解决

7. 查看绑定结果

```
cat/proc/net/bonding/bond0
Ethernet Channel BondingDriver: v3.7.1 (April 27, 2011)
  
Bonding Mode: load balancing(round-robin)
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0
  
Slave Interface: eth0
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr:00:50:56:28:7f:51
Slave queue ID: 0
  
Slave Interface: eth1
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr:00:50:56:29:9b:da
Slave queue ID: 0
```

