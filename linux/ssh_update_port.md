1. 编辑配置文件修改端口

```bash
vim /etc/ssh/sshd_confg
#Port 22
Port 20022
```

2. 关闭selinux，或者执行一下命令

```bash
semanage port -a -t ssh_port_t -p tcp 20022
```

