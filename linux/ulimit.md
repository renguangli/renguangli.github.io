### ulimit -a

通过 ulimit -a 查看所有的资源上限：

```
[renguangli@localhost ~]$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 7271
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 65535
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 4096
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

- -s 栈大小: 8m

- -u 进程上限: 4096

- -n 文件描述符上限: 65535

### 修改文件描述符

临时修改

```bash
[root@localhost ~]# ulimit -n
65535
[root@localhost ~]# ulimit -n 102400
[root@localhost ~]# ulimit -n
102400
```

永久修改

```bash
[root@localhost ~]# vi /etc/security/limits.conf
## root 用户
root soft nofile 65535
root hard nofile 65535
## 所有用户
* soft nofile 65535
* hard nofile 65535

sysctl -p
```

### 系统层的一些资源限制

单个进程打开文件句柄数上限 最大文件描述符数 100多W。

```
[renguangli@localhost ~]$ cat /proc/sys/fs/nr_open 
1048576
```

系统分配的pid上限是 32768。

```
[renguangli@localhost ~]$ cat /proc/sys/kernel/pid_max 
32768
```

file-max 是在内核级别强制执行的最大文件描述符（FD），上限 183979。

```
[renguangli@localhost ~]$ cat /proc/sys/fs/file-max 
183979
```

已分配的文件文件描述符数，已分配但未使用的文件描述符数以及最大文件描述符数(不可调)。

```
[renguangli@localhost ~]$ cat /proc/sys/fs/file-nr 
1216    0    183979
```

系统总线程数限制为 14543。

```
[renguangli@localhost ~]$ cat /proc/sys/kernel/threads-max 
14543
```

### prlimit

类似 ulimit 的命令

```
[renguangli@localhost ~]$ prlimit
RESOURCE   DESCRIPTION                             SOFT      HARD UNITS
AS         address space limit                unlimited unlimited bytes
CORE       max core file size                         0 unlimited blocks
CPU        CPU time                           unlimited unlimited seconds
DATA       max data size                      unlimited unlimited bytes
FSIZE      max file size                      unlimited unlimited blocks
LOCKS      max number of file locks held      unlimited unlimited 
MEMLOCK    max locked-in-memory address space     65536     65536 bytes
MSGQUEUE   max bytes in POSIX mqueues            819200    819200 bytes
NICE       max nice prio allowed to raise             0         0 
NOFILE     max number of open files               65535     65535 
NPROC      max number of processes                 4096      7271 
RSS        max resident set size              unlimited unlimited pages
RTPRIO     max real-time priority                     0         0 
RTTIME     timeout for real-time tasks        unlimited unlimited microsecs
SIGPENDING max number of pending signals           7271      7271 
STACK      max stack size                       8388608 unlimited bytes
```
