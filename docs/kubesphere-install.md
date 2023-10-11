# kubesphere3.2.0 安装记录

## 基础环境

1. 关闭 firewalld，selinux

```
setenforce 0
vi /etc/selinux/config
##SELINUX=enforcing
SELINUX=disabled
```

1. 关闭swapoff
2. 安装基础软件

```
yum install ebtables socat ipset conntrack
```

## 安装 docker

下载地址：https://download.docker.com/linux/static/stable/x86_64/docker-20.10.9.tgz

```
tail -xf docker-20.10.9.tgz
mv docker/* /usr/bin
 
```

vi /etc/systemd/system/docker.service

```
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
# After=network-online.target firewalld.service containerd.service
# Wants=network-online.target
# Requires=docker.socket containerd.service

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
TimeoutSec=0
RestartSec=2
Restart=always

# Note that StartLimit* options were moved from "Service" to "Unit" in systemd 229.
# Both the old, and new location are accepted by systemd 229 and up, so using the old location
# to make them work for either version of systemd.
StartLimitBurst=3

# Note that StartLimitInterval was renamed to StartLimitIntervalSec in systemd 230.
# Both the old, and new name are accepted by systemd 230 and up, so using the old name to make
# this option work for either version of systemd.
StartLimitInterval=60s

# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity

# Comment TasksMax if your systemd version does not support it.
# Only systemd 226 and above support this option.
TasksMax=infinity

# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes

# kill only the docker process, not all processes in the cgroup
KillMode=process
OOMScoreAdjust=-500

[Install]
WantedBy=multi-user.target
```

vi /etc/docker/daemon.json

```
{
  "insecure-registries": ["dockerhub.kubekey.local"],
  "log-opts": {
    "max-size": "5m",
    "max-file":"3"
  },
  "exec-opts": ["native.cgroupdriver=systemd"]
}
```

```
systemctl start docker
systemctl enable docker
```

## 搭建私有镜像仓库

```
mkdir -p certs

openssl req \
-newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key \
-x509 -days 36500 -out certs/domain.crt

docker run -d \
  --restart=always \
  --name registry \
  -v "$(pwd)"/certs:/certs \
  -v /mnt/registry:/var/lib/registry \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -p 443:443 \
  registry:2
```

hosts

```
# docker registry
192.168.0.2 dockerhub.kubekey.local
```

```
mkdir -p  /etc/docker/certs.d/dockerhub.kubekey.local
```

```
cp certs/domain.crt  /etc/docker/certs.d/dockerhub.kubekey.local/ca.crt
```



ipvsadm -a -t 172.16.22.219:443 -r registry-1.docker.io:443 -g 

```
./offline-installation-tool.sh -l images-list.txt -d ./kubesphere-images -r dockerhub.kubekey.local

```

## 安装 

```
./kk create config --with-kubernetes v1.21.5 --with-kubesphere v3.2.0 -f config-sample.yaml

./kk create cluster -f 
```

## 添加节点

```
./kk add nodes -f config-sample.yaml
```

