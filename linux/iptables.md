```bash
iptables -I INPUT  -p tcp  -m iprange --src-range 24.43.158.16-24.43.158.20  -m multiport --dport 2181 -j ACCEPT
iptables -A INPUT  -p tcp  -m multiport --dport 2181 -j DROP
```

限制只允许某些ip访问

```
iptables -I INPUT  -p tcp  -m iprange --src-range 24.43.158.45-24.43.158.48  -m multiport --dport 1883 -j ACCEPT
iptables -I INPUT  -p tcp  -s 24.43.158.41  -m multiport --dport 1883 -j ACCEPT
iptables -I INPUT  -p tcp  -s 24.43.158.53  -m multiport --dport 1883 -j ACCEPT
iptables -A INPUT  -p tcp  -m multiport --dport 1883 -j DROP
```

```bash
iptables -I INPUT -j DROP  # 阻止所有机器访问本机
iptables -A INPUT -s 192.168.88.0/24 -p tcp -j ACCEPT
iptables -A INPUT -s 192.169.0.0/24 -p tcp -j ACCEPT
iptables -A INPUT -s 10.119.4.0/24 -p tcp -j ACCEPT
iptables -A INPUT -s 10.10.10.0/24 -p tcp -j ACCEPT
```

```bash
iptables -L  # 查看规则链
iptables -F  # 清除规则链，只剩默认策略
iptables -I INPUT -j DROP  # 阻止所有机器访问本机
iptables -t filter -A INPUT -p icmp -j REJECT  # 不允许任何主机 ping 本主机，REJECT 拒绝
iptables -I INPUT 1 -p icmp --icmp-type echo-request -j DROP  # 禁止全部 ping 操作，同上
iptables -I INPUT -p icmp --icmp-type echo-request -s 192.168.10.30 -j ACCEPT  # 允许指定 ip 可以 ping 操作
iptables -A INPUT -s 192.168.1.0/24 -p tcp -j ACCEPT  # 配置允许某一网段访问本机所有端口
iptables -I INPUT -p tcp --dport 22 -j DROP  # 拒绝所有主机访问本机 tcp 22 端口
iptables -I INPUT -s 192.168.10.30 -p tcp --dport 22 -j ACCEPT  # 允许指定主机访问本机 tcp 22 端口
iptables -A INPUT -p tcp --dport 22 -j ACCEPT  # 配置允许所有 IP 访问本机 22 端口
iptables -I INPUT -p tcp -m iprange --src-range 192.168.10.10-192.168.10.100 -j ACCEPT  # 允许指定范围 IP 访问本机所有 tcp 端口
iptables -A INPUT -s 192.168.1.123 -p tcp --dport 80 -j DROP  # 配置禁止某个 ip 访问本机的 80 端口
service iptables save  # 使配置防火墙策略永久生效，执行保存命令，不然的话重启后会失效
```
