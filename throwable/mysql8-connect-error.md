# 

链接 MySQL8.0 时报错

```
Public Key Retrieval is not allowed
```

配置文件中加上 `allowPublicKeyRetrieval=true` 参数

```
spring.datasource.url=jdbc:mysql://localhost:3306/rainy?useSSL=false&allowPublicKeyRetrieval=true
```

