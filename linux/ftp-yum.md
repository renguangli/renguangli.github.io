### 搭建基于 ftp 的 yum 源

1. 将 CentOS-7-x86_64-DVD-1810.iso 镜像上传至服务器 10.119.4.14:/root 目录下
2. 挂载 CentOS-7-x86_64-DVD-1810.iso 到 /mnt 目录

```
mount -o loop /mnt /root/CentOS-7-x86_64-DVD-1810.iso
```

3. 创建 local.repo

```
cd /etc/yum.repo.d
vi local.repo
[local]
name=local-yum
baseurl=file:///mnt
enable=1
gpgcheck=0
```

4. 安装 ftp

```
yum install -y vsftpd
```

5. 启动 ftp

```
systemctl start vsftpd
systemctl enable vsftpd
```

6. 将 mnt 下的文件拷贝到 /var/ftp/pub 目录下

```
cp -r /mnt/* /var/ftp/pub
```

7. 其他服务器搭建 ftp yum源

```
cd /etc/yum.repo.d
mkdir bak
mv Centos* /bak
vi ftp.repo
[ftp]
name=ftp-yum
baseurl=ftp://10.119.4.14/pub
enable=1
gpgcheck=0
```
