# 用 Docker 部署 Seafile 服务

## 快速开始

### 安装 Docker

因为 Seafile v7.x.x 及以后版本容器是通过 Docker 运行的，所以您应该先在服务器上安装 Docker。

 ﻿﻿[CentOS 中安装 Docker](https://docs.docker.com/engine/install/centos/)﻿﻿

 ﻿﻿[Ubuntu 中安装 Docker](https://docs.docker.com/engine/install/ubuntu/)﻿

### 安装 docker-compose

因为 Seafile v7.x.x 容器是通过 docker-compose 命令运行的，所以您应该先在服务器上安装该命令。

```
# for CentOSyum install docker-compose -y﻿
# for Ubuntuapt-get install docker-compose -y
TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
```

### 下载并修改 docker-compose.yml

下载 ﻿[docker-compose.yml](https://docs.seafile.com/d/cb1d3f97106847abbf31/files/?p=/docker/docker-compose.yml)﻿ 示例文件到您的服务器上，然后根据您的实际环境修改该文件。尤其是以下几项配置：

- MySQL root 用户的密码 (MYSQL_ROOT_PASSWORD and DB_ROOT_PASSWD)
- 持久化存储 MySQL 数据的 volumes 目录 (volumes)
- 持久化存储 Seafile 数据的 volumes 目录 (volumes)

### 启动 Seafile 服务

执行以下命令启动 Seafile 服务

```
docker-compose up -d
TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
```

需要等待几分钟，等容器首次启动时的初始化操作完成后，您就可以在浏览器上访问`http://seafile.example.com` 来打开 Seafile 主页。

**注意：您应该在** `docker-compose.yml` **文件所在的目下执行以上命令。**

## 更多配置项

### 自定义管理员用户名和密码

默认的管理员账号是 `me@example.com` 并且该账号的密码是 `asecret`，您可以在 `docker-compose.yml` 中配置不同的用户名和密码，为此您需要做如下配置：

```
seafile:    ...﻿
    environment:        ...        - SEAFILE_ADMIN_EMAIL=me@example.com        - SEAFILE_ADMIN_PASSWORD=a_very_secret_password        ...
TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
```

### 修改 Seafile 服务的配置

Seafile 的配置文件存放在 `shared/seafile/conf` 目录下，您可以根据﻿[Seafile 手册](https://manual-cn.seafile.com/)﻿的指导来修改这些配置项。

一旦修改了配置文件，您需要重启服务以使其生效：

```
docker-compose restart
TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
```

### 查找日志

您可以使用以下命令来查看 Seafile Docker 的日志

```
docker-compose logs -f
TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
```

Seafile 容器中 Seafile 服务本身的日志文件存放在 `/shared/logs/seafile` 目录下，或者您可以在宿主机上 Seafile 容器的卷目录中找到这些日志，例如：`/opt/seafile-data/logs/seafile`

同样 Seafile 容器的系统日志存放在 `/shared/logs/var-log` 目录下，或者宿主机目录 `/opt/seafile-data/logs/var-log`。

### 增加一个新的管理员

确保各容器正常运行，然后执行以下命令：

```
docker exec -it seafile /opt/seafile/seafile-server-latest/reset-admin.sh
TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
```

根据提示输入用户名和密码，您现在有了一个新的管理帐户。

## Seafile 目录结构

### `/shared`

共享卷的挂载点,您可以选择在容器外部存储某些持久性信息.在这个项目中，我们会在外部保存各种日志文件和上传数据。 这使您可以轻松重建容器而不会丢失重要信息。

- /shared/seafile: Seafile 服务的配置文件以及数据文件
- /shared/logs: 日志目录
  - /shared/logs/var-log: 我们将容器内的`/var/log`链接到本目录。您可以在`/shared/logs/var-log/nginx/`中找到 nginx 的日志文件
  - /shared/logs/seafile: Seafile 服务运行产生的日志文件目录。比如您可以在 `/shared/logs/seafile/seafile.log` 文件中看到 seaf-server 的日志
- /shared/ssl: 存放证书的目录，默认不存在

### 升级 Seafile 服务

如果要升级 Seafile 服务到最新版本：

```
docker pull seafileltd/seafile-mc:latestdocker-compose downdocker-compose up -d
TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
```

## 备份和恢复

### 目录结构

我们假设您的 seafile 数据卷路径是 `/opt/seafile-data`，并且您想将备份数据存放到 `/opt/seafile-backup` 目录下。

您可以创建一个类似以下 `/opt/seafile-backup` 的目录结构：

```
/opt/seafile-backup---- databases/  用来存放 MySQL 容器的备份数据---- data/  用来存放 Seafile 容器的备份数据
TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
```

要备份的数据文件：

```
/opt/seafile-data/seafile/conf  # configuration files/opt/seafile-data/seafile/seafile-data # data of seafile/opt/seafile-data/seafile/seahub-data # data of seahub
TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
```

### 备份数据

 步骤：

1. 备份 MySQL 数据库数据；
2. 备份 Seafile 数据目录；

- 备份数据库：

  \# 建议每次将数据库备份到一个单独的文件中。至少在一周内不要覆盖旧的数据库备份。

  ```
  cd /opt/seafile-backup/databasesdocker exec -it seafile-mysql mysqldump  -uroot -p** --opt ccnet_db > ccnet_db.sqldocker exec -it seafile-mysql mysqldump  -uroot -p** --opt seafile_db > seafile_db.sqldocker exec -it seafile-mysql mysqldump  -uroot -p** --opt seahub_db > seahub_db.sql
  TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
  ```

  注释：-p 后面是mysql的root用户登录密码，不要有空格

- 备份 Seafile 资料库数据：

  - 直接复制整个数据目录

    ```
    cp -R /opt/seafile-data/seafile /opt/seafile-backup/data/cd /opt/seafile-backup/data && rm -rf ccnet
    TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
    ```

  - 使用 rsync 执行增量备份

    ```
    rsync -az /opt/seafile-data/seafile /opt/seafile-backup/data/cd /opt/seafile-backup/data && rm -rf ccnet
    TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
    ```

### 恢复数据

- 恢复数据库：

  ```
  docker cp /opt/seafile-backup/databases/ccnet_db.sql seafile-mysql:/tmp/ccnet_db.sqldocker cp /opt/seafile-backup/databases/seafile_db.sql seafile-mysql:/tmp/seafile_db.sqldocker cp /opt/seafile-backup/databases/seahub_db.sql seafile-mysql:/tmp/seahub_db.sql﻿
  docker exec -it seafile-mysql /bin/sh -c "mysql -uroot -p** ccnet_db < /tmp/ccnet_db.sql"docker exec -it seafile-mysql /bin/sh -c "mysql -uroot -p** seafile_db < /tmp/seafile_db.sql"docker exec -it seafile-mysql /bin/sh -c "mysql -uroot -p** seahub_db < /tmp/seahub_db.sql"
  TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
  ```

- 恢复 seafile 数据：

  ```
  cp -R /opt/seafile-backup/data/* /opt/seafile-data/seafile/
  TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
  ```

## 垃圾回收

在 seafile 中，当文件被删除时，组成这些文件的块数据不会立即删除，因为可能有其他文件也会引用这些块数据(用于去重功能的实现)。为了真正删除无用的块数据，还需要额外运行"﻿[GC](https://manual-cn.seafile.com/maintain/seafile_gc.html)﻿"程序。GC 会自动检测到哪些数据块不再被任何文件所引用，并清除它们。

GC 脚本被放在docker容器的 `/scripts` 目录下。执行 GC 的方法很简单：`docker exec seafile /scripts/gc.sh`。对于社区版来说，该程序会暂停 Seafile 服务，但这是一个相对较快的程序，一旦程序运行完成，Seafile 服务也会自动重新启动。而专业版提供了在线运行 GC 的功能，不会暂停 Seafile 服务。

## 常见问题

**您可以运行 **`docker exec`之类的docker命令来查找错误。

```
docker exec -it seafile /bin/bash
TextHTMLCSSJavascriptCC++C#JavaPythonSqlSwift
```

