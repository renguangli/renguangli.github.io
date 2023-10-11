## redis 安装

```
## 解压源码
tar -xf redis-6.2.6.tar.gz
## 编译并安装
cd redis-6.2.6
make && make install 
## 安装成系统服务
cd utils
./install_server.sh
## 修改配置文件
vim /etc/redis/6379.conf
daemonize yes ## 开启远程连接
requirepass Bonc@123 ## 添加密码
service redis_6379 start ## 重启redis
```

