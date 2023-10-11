## 系统日志采集配置

在配置文件`/et/rsyslog.conf`末尾添加日志推送地址

```
vi /et/rsyslog.conf
*.* @10.119.4.87:5142
```

重启系统日志服务

```
systemctl restart rsyslog
systemctl enable rsyslog
```

