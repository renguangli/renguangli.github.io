MySQL 安装完成后，首次登陆报以下错误

```
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
```

因为我们首次登陆使用的 MySQL 初始化始生成的临时密码，所以需要我们修改 root 用户密码。

```
alter user user() identified by 'root';
```

或者

```
 alter user 'root'@'localhost' identified by 'root';
```

如果密码设置了过期时间且密码已经到了过期时间也会报这个错误，解决方法同样是修改密码。

