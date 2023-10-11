Ntp 升级

下载源码包，解压，编译

```
./configure
make && make install
默认安装之 /usr/local/bin
```



```
cd ntp-bin
chmod +x *
mv * /usr/sbin
```

 vim /usr/lib/systemd/system/ntpd.service

```
/usr/sbin/ntpd -u ntp:ntp $OPTIONS   // 去掉 -u ntp:ntp
```

```
systemctl daemon-reload
systemctl restart ntpd
ntpd --version
ntpd 4.2.8p15@1.3728 Mon Nov 29 10:40:58 UTC 2021 (2)
```



Mdns   5353 漏洞

```
[root@influxdb bin]# netstat -naup |grep 5353
udp        0      0 0.0.0.0:5353            0.0.0.0:*                           14609/avahi-daemon:

systemctl stop avahi-daemon
systemctl disable avahi-daemon
```

RPCbind

```
[root@influxdb bin]# netstat -naup |grep 776
udp        0      0 0.0.0.0:776             0.0.0.0:*                           14597/rpcbind       
udp6       0      0 :::776                  :::*                                14597/rpcbind       
[root@influxdb bin]# systemctl stop rpcbind
Warning: Stopping rpcbind.service, but it can still be activated by:
  rpcbind.socket
[root@influxdb bin]# systemctl diable rpcbind
Unknown operation 'diable'.
[root@influxdb bin]# systemctl disable rpcbind
Removed symlink /etc/systemd/system/multi-user.target.wants/rpcbind.service.
```

