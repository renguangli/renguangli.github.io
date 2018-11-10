---
layout: post
title: MySQL 重置 root 用户密码 - 忘记密码
categories: MySQL
tags: [MySQL]
---

如果忘记了 MySQL root 用户密码，如何重置？

## MySQL 重置 root 用户密码

### 在配置文件 /etc/my.cnf 添加配置，重启 MySQL

```
## 无密码登录
skip-grant-tables
```

### 登陆 MySQL 客户端，不需要密码，选择 mysql 库

```bash
mysql -uroot -p
use mysql;
```

### 修改 MySQL root 用户密码

5.7 版本之后已经没有了 password 字段，而是用 authentication_string 加密字段代替

```sql
update user set authentication_string=password('root') where user='root';
```

5.7 版本以前
```sql
update user set password=password('root') where user='root';
```
