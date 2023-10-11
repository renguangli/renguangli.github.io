修改完 Nginx 配置后，执行 `nginx -s reload` 命令后报了如下错误

```
nginx: [error] open() "/run/nginx.pid" failed (2: No such file or directory)
```

`nginx -s reload` 命令需要通过 nginx.pid 获取 master 进程号，会去找 /var/run/nginx.pid ,如果 nginx.pid 不 存在就报 nginx.pid 文件找不到的错误

出现这种错误的原因有两种，第一种就是 Nginx 还没启动，执行 `nginx` 启动命令就可以了

另一种就是 Nginx 已经启动了，误操作把 nginx.pid 文件给删除了，这种情况有两种方法解决。

1. 杀掉 Nginx 重新启动

找到 Nginx 进程，然后杀掉，重启

```
[root@localhost ~]# ps -ef|grep nginx
root     22919     1  0 20:06 ?        00:00:00 nginx: master process nginx
nginx    22920 22919  0 20:06 ?        00:00:00 nginx: worker process
root     22930 22819  0 20:06 pts/0    00:00:00 grep --color=auto nginx
[root@localhost ~]# kill -9 22919 22920
[root@localhost ~]# nginx
[root@localhost ~]#
```

如果不想杀掉重启可以用第二种方法

2. 新建 nginx.pid 文件

查看 Nginx 主进程号

```
[root@localhost ~]# ps -ef|grep nginx
root     22932     1  0 20:06 ?        00:00:00 nginx: master process nginx
nginx    22933 22932  0 20:06 ?        00:00:00 nginx: worker process
root     22943 22819  0 20:07 pts/0    00:00:00 grep --color=auto nginx
```

Nginx 主进程号为 22932

新建n ginx.pid 文件

```
touch /var/run/nginx.pid
```

将主进程号写入 nginx.pid 文件

```
[root@localhost ~]# echo 22932 >  /var/run/nginx.pid
```

重新加载配置文件

```
[root@localhost ~]# nginx -s reload
[root@localhost ~]#
```

`nginx -s reload`命令执行成功