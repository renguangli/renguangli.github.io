```
./kk create config --with-kubernetes v1.21.5 --with-kubesphere v3.2.0 -f config-sample.yaml

./kk add nodes -f conf.json


docker: error adding content digest to lease: sha256:31168c113862cce4cef6b16b20cdef1b126eb755492a6030ca68a9020b7eb657: unknown method AddResource: not implemented.

```



```
docker: error adding content digest to lease: sha256:31168c113862cce4cef6b16b20cdef1b126eb755492a6030ca68a9020b7eb657: unknown method AddResource: not implemented.

yum list |grep docker 

yum remove @docker

systemctl restart docker
systemctl restart containerd
```





```
[root@node01 kubesphere-3.2.0]# kubectl get nodes
NAME     STATUS   ROLES                         AGE     VERSION
node01   Ready    control-plane,master,worker   16m     v1.21.5
node02   Ready    worker                        15m     v1.21.5
node03   Ready    worker                        15m     v1.21.5
node04   Ready    worker                        15m     v1.21.5
node05   Ready    worker                        15m     v1.21.5
node06   Ready    <none>                        6m14s   v1.21.5
```

roles 显示 none

解决方法

kubectl label node node06 node-role.kubernetes.io/worker=worker