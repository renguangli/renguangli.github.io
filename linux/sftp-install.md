### 创建用户组

```
groupadd sftp
```

### 创建用户并设置密码

```
useradd -g sftp -M -s /sbin/nologin  ncepri
passwd  ncepri
```

### 创建/data/sftp/ncepri目录，并将它指定为ncepri用户的home目录

``` bash
mkdir -p /data/sftp/ncepri
usermod -d /data/sftp/ncepri  ncepri
```

### 设置Chroot目录权限

```
chown root:sftp /data/sftp/ncepri
chmod 755 /data/sftp/ncepri
```

### 新建一个目录供sftp用户ncepri上传文件，这个目录所有者为ncepri所有组为sftp，所有者有写入权限所有组无写入权限

```
mkdir -p /data1/sftp/ncepri/uploads
chown -R ncepri:sftp /data1/sftp/ncepri/uploads
chmod 755 /data1/sftp/ncepri/uploads
```

### 修改配置文件，重启sshd

vim /etc/ssh/sshd_config

```
Subsystem       sftp    internal-sftp
Match Group sftp
ChrootDirectory /data/sftp/%u
X11Forwarding no
AllowTcpForwarding no
ForceCommand internal-sftp
```

```
systemctl restart sshd
```

