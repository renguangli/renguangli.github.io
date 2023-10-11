influxdb 添加 basic 认证

1. 添加 admin 账户

```
influx
create user "admin" with password 'admin' with all privileges;
```

2. 修改配置文件，auth-enabled 修改为 true，修改完后重启

```
[root@localhost influxdb]# vi influxdb.conf
[http]
  enabled = true
  bind-address = ":8086"
  auth-enabled = true
```

3. influx 命令行连接 

```
[root@localhost influxdb]# influx -username admin -password admin
// 或者
[root@localhost influxdb]# ./influx
Connected to http://localhost:8086 version unknown
InfluxDB shell version: unknown
> auth
username: admin
password:
```

4. http 接口调用添加认证

```
[root@localhost influxdb]# curl "http://192.168.10.11:8086/query?u=admin&p=admin&q=show%20databases"
{"results":[{"statement_id":0,"series":[{"name":"databases","columns":["name"],"values":[["_internal"]]}]}]}
```

