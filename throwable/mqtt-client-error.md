使用mqtt客户端接收数据报一下错误

```bash
[root@python1 linuxbin]# ./datarecv -h 10.119.4.119 -p 1883 -u fgc@fengguangchu -P fgc1234 -t /iot/v3/fengguangchu/jinyang/U/P/DJV1/1 -j 1 | grep 011261609
./datarecv: error while loading shared libraries: libmosquitto.so: cannot open shared object file: No such file or directory
```

原因是无法加载动态库

解决办法
添加环境变量
```bash
[root@python1 linuxbin]# vi /etc/profile
export LD_LIBRARY_PATH=/home/linuxbin
[root@python1 linuxbin]# source /etc/profile
[root@python1 linuxbin]# . /etc/profile
```

添加环境变量后，接收成功

```bash
[root@python1 linuxbin]# ./datarecv -h 10.119.4.119 -p 1883 -u fgc@fengguangchu -P fgc1234 -t /iot/v3/fengguangchu/jinyang/U/P/DJV1/1 -j 1 | grep 011261609
01126160970011122448,2021-06-02 15:19:05.370,1,none,0.000000,none
01126160970011121540,2021-06-02 15:19:05.371,1,none,0.000000,none
```
