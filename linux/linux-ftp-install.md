## 安装 ftp

```
yum install vsftpd
```

## 修改配置文件

```
vim /etc/vsftpd/vsftpd.conf
anonymous_enable=NO  ## YES 改为 NO
chroot_local_user=YES ## 打开注释
chroot_list_enable=YES ## 打开注释
chroot_list_file=/etc/vsftpd/chroot_list ## 添加
## 添加
allow_writeable_chroot=YES 
## 添加
pasv_promiscuous=YES
```

## 创建 chroot_list 文件

```
touch /etc/vsftpd/chroot_list
```

## 不允许 ssh 登录

```
vi /etc/shells
/sbin/nologin
```

## 创建用户

```
useradd -s /sbin/nologin -d /data/fgc fgc
passwd fgc 
```

## 遇到的问题

1. vsftpd登录时提示“vsftpd 500 OOPS: chroot”的问题

   ```
   [root@localhost fgc]# ftp localhost
   Trying ::1...
   Connected to localhost (::1).
   220 (vsFTPd 3.0.2)
   Name (localhost:root): fgc
   331 Please specify the password.
   Password:
   500 OOPS: chroot
   Login failed.
   421 Service not available, remote server has closed connection
   ```

关闭 selinux

1、临时关闭：

输入命令`setenforce 0`即可，但重启系统后还会开启。

2、永久关闭：

输入命令`vi /etc/selinux/config`打开config文件

将`SELINUX=enforcing`改为`SELINUX=disabled`

然后保存退出。
