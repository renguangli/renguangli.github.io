## 自签名证书

生成CA根证书

```
openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.crt

3) 安装CA根证书
设置->隐私设置和安全性->安全->管理证书->受信任的根证书颁发机构->导入生成的证书（rootCA.crt）
```

生成CA自签证书

```
openssl genrsa -out server.key 2048
openssl req -new -key server.key -out server.csr -extensions v3_req
注意：此时除Common Name (eg, YOUR name) []外需要输入与创建CA根证书时相同的信息。此处Common Name 应该输入服务器（Server）的Ip或域名（与在浏览器地址栏需要访问的保持一致）

使用CA根证书签名CSR server.crt 的时间期限（-days）不能超过CA根证书的时间期限
openssl x509 -req -in server.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out server.crt -days 500 -sha256 -extensions v3_req -extfile openssl.cnf

```

