进入解压出来的nginx **源码**目录

```
 vi src/http/ngx_http_header_filter_module.c  # 49-50行
```

编辑:

```
内容：
static char ngx_http_server_string[] = "Server: nginx" CRLF;
static char ngx_http_server_full_string[] = "Server: " NGINX_VER CRLF;

更改为：
static char ngx_http_server_string[] = "Server: X-Web" CRLF;
static char ngx_http_server_full_string[] = "Server:X-Web " CRLF;
```



```
 ./configure --with-http_ssl_module  --prefix=/data/nginx
make && make install
```

