ntp时间同步服务器搭建

### 安装 ntpd

```bash
yum install ntpd
```

### 阿里云NTP时间同步服务器域名

times.aliyun.com

ntp.aliyun.com

### 使用ntpdate同步时间服务器

```bash
[root@localhost ~]# ntpdate times.aliyun.com
23 Jul 10:34:48 ntpdate[21378]: adjust time server 120.25.115.20 offset -0.000329 sec
```

### ntp 服务配置

/etc/ntp.conf 默认配置内容如下

```
[root@vm ~]# vi /etc/ntp.conf
#系统时间与BIOS事件的偏差记录
driftfile /var/lib/ntp/drift

restrict default kod nomodify notrap nopeer noquery    # 拒绝所有IPv4的client连接此NTP服务器
restrict -6 default kod nomodify notrap nopeer noquery    # 拒绝所有IPv6的client连接此NTP服务器

restrict 127.0.0.1    # 放行本机localhost对NTP服务的访问
restrict -6 ::1

# Hosts on local network are less restricted.
#restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap    # 放行192.168.1.0网段主机与NTP服务器进行时间同步

# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).

server 0.centos.pool.ntp.org iburst    # 代表的同步时间服务器
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst

#broadcast 192.168.1.255 autokey        # broadcast server
#broadcastclient                        # broadcast client
#broadcast 224.0.1.1 autokey            # multicast server
#multicastclient 224.0.1.1              # multicast client
#manycastserver 239.255.254.254         # manycast server
#manycastclient 239.255.254.254 autokey # manycast client

# Enable public key cryptography.
#crypto

includefile /etc/ntp/crypto/pw

# Key file containing the keys and key identifiers used when operating
# with symmetric key cryptography. 
keys /etc/ntp/keys
```
