## 开放端口

```
firewall-cmd --zone=public --add-port=5672/tcp --permanent # 开放5672端口
firewall-cmd --zone=public --remove-port=5672/tcp --permanent #关闭5672端口
firewall-cmd --reload  # 配置立即生效
```

## 查看防火墙所有开放的端口

```
firewall-cmd --zone=public --list-ports
```

## 关闭防火墙

如果要开放的端口太多，可以关闭防火墙，安全性自行评估

```
systemctl stop firewalld.service
```

## 查看防火墙状态

```
firewall-cmd --state
systemctl status firewalld.service
```

