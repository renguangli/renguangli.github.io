```
cd /usr/local/
mkdir mysql
tar -xf mysql-*.tar.gz
mv mysql-*.tar.gz mysql
vi /etc/my.cnf
[mysql]
default-character-set=UTF8MB4

[mysqld]
user=_mysql
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
port = 3306
pid-file=/usr/local/mysql/data/mysqld.pid
socket = /tmp/mysql.sock

character-set-server=UTF8MB4
lower_case_table_names=2

sudo bin/mysqld --initialize --console
support-file/mysql.server start
```

