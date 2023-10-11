下载源码以及boost包

修改版本号

```
cat version
MYSQL_VERSION_MAJOR=5
MYSQL_VERSION_MINOR=7
MYSQL_VERSION_PATCH=40
MYSQL_VERSION_EXTRA=
```

## cmake

```
cmake -DCMAKE_INSTALL_PREFIX=/root/mysql-5.7.40 -DWITH_BOOST=/root/mysql-5.7.40/boost
```

## make

```
make
```

## make install

```
make instal
```

## 替换文件

替换掉已安装的mysql的中 mysql 以及 mysqld 文件

## 重启mysql

service mysqld start