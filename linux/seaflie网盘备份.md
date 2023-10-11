增量备份

```
rsync -az /data/seafile/data/seafile/  /data/seafile-backup/data/
cd /data/seafile-backup/data/seafile && rm -rf ccnet
```

远程同步
```
rsync -az /data/seafile/data/seafile/  '-e ssh -l root -p 10022' 24.43.158.31:/data/disk03/seafile-backup/data
ssh root@24.43.158.31 "cd /data/disk03/seafile-backup/data && rm -rf ccnet"
```


备份数据库

```
cd /data/seafile/sql-bakup
docker exec -it seafile-mysql mysqldump  -uroot -pdb_dev --opt ccnet_db > ccnet_db.sql
docker exec -it seafile-mysql mysqldump  -uroot -pdb_dev --opt seafile_db > seafile_db.sql
docker exec -it seafile-mysql mysqldump  -uroot -pdb_dev --opt seahub_db > seahub_db.sql
```

备份脚本

```
#!/bin/sh
rsync -az /data/seafile/data/seafile/  '-e ssh -l root -p 10022' 24.43.158.31:/data/disk03/seafile-backup/data/seafile
ssh -p 10022 root@24.43.158.31 "cd /data/disk03/seafile-backup/data/seafile && rm -rf ccnet"

## 备份数据库
bak_path=/data/seafile/sql-bakup
bak_time=`date +%Y%m%d`
if [[ ! -d ${bak_path}/${bak_time} ]]; then
  mkdir ${bak_path}/${bak_time}
fi
docker exec -it seafile-mysql mysqldump  -uroot -pdb_dev --opt ccnet_db > ${bak_path}/${bak_time}/ccnet_db.sql
docker exec -it seafile-mysql mysqldump  -uroot -pdb_dev --opt seafile_db > ${bak_path}/${bak_time}/seafile_db.sql
docker exec -it seafile-mysql mysqldump  -uroot -pdb_dev --opt seahub_db > ${bak_path}/${bak_time}/seahub_db.sql

## 删除7天之前的北非
date_7=$(date -d -7day +%Y%m%d)
rm -rf ${bak_path}/${date_7}

## 同步至远程服务器
rsync -az ${bak_path}/  '-e ssh -l root -p 10022' 24.43.158.31:/data/disk03/seafile-backup/databases
```

恢复数据

将备份服务器的`24.43.158.31:/data/disk03/seafile-backup/data/seafile`目录下文件拷贝到本地`24.43.158.98://data/seafile/data/seafile` 

恢复sql

```
docker cp /data/seafile-backup/databases/ccnet_db.sql seafile-mysql:/tmp/ccnet_db.sql
docker cp /data/seafile-backup/databases/seafile_db.sql seafile-mysql:/tmp/seafile_db.sql
docker cp /data/seafile-backup/databases/seahub_db.sql seafile-mysql:/tmp/seahub_db.sql

docker exec -it seafile-mysql /bin/sh -c "mysql -uroot -ppdb_dev ccnet_db < /tmp/ccnet_db.sql"
docker exec -it seafile-mysql /bin/sh -c "mysql -uroot -ppdb_dev seafile_db < /tmp/seafile_db.sql"
docker exec -it seafile-mysql /bin/sh -c "mysql -uroot -ppdb_dev seahub_db < /tmp/seahub_db.sql"
```

