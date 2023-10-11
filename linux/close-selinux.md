1、查看

```
[root@dev-server ~]# getenforce
Disabled
[root@dev-server ~]# /usr/sbin/sestatus -v
SELinux status:                 disabled
```

2、临时关闭

```
##设置SELinux 成为permissive模式
setenforce 1 
setenforce 0
```

3、永久关闭

将 SELINUX=enforcing 改为 SELINUX=disabled

```
vi /etc/selinux/config
##SELINUX=enforcing
SELINUX=disabled
```

设置后需要重启才能生效