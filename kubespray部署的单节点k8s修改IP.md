## 1. 备份/etc/kubernetes目录
```shell
cp /etc/kubernetes /etc/kubernetes.bak
```



## 2. 替换所有文件中的IP为新地址

grep 192.168.56.112 -rl *|xargs sed -i 's/192.168.56.112/192.168.56.121/g'



## 3. 备份etcd证书文件 以及相关配置文件

cp /etc/ssl/etcd/ /etc/ssl/etcd.bak

cp /etc/etcd.env /etc/etcd.env.bak

cp /etc/systemd/system/etcd.service /root/etcd.service



## 4. 停止kubelet,etcd服务,删除etcd证书,删除其他etcd配置文件

systemctl stop kubelet&& systemctl stop etcd && rm  -rf /etc/systemd/system/etcd.service && rm -rf /etc/ssl/etcd/ && rm -rf /etc/etcd.env

systemctl daemon-reload



## 5. 修改IP地址

vi /etc/sysconfig/network-scripts/ifcfg-enp0s8

systemctl restart network



## 6. 修改inventory文件，重新跑etcd tag

ansible-playbook -i inventory.ini cluster.yml --tags=etcd



## 7. 删除旧的证书文件与配置文件

rm -rf /etc/kubernetes/ssl/apiserver*

rm -rf /etc/kubernetes/ssl/front-proxy*

rm -rf /etc/kubernetes/*.conf



## 8. 重新生成新的证书与配置

kubeadm init phase certs all  --config /etc/kubernetes/kubeadm-config.yaml

kubeadm init phase kubeconfig all --config /etc/kubernetes/kubeadm-config.yaml



## 9. 重启api-server,controller-manager,scheduler

docker ps |grep kube-apiserver|grep -v pause|awk '{print $1}'|xargs -i docker restart {}

docker ps |grep kube-controller-manager|grep -v pause|awk '{print $1}'|xargs -i docker restart {}

docker ps |grep kube-scheduler|grep -v pause|awk '{print $1}'|xargs -i docker restart {}



## 10. 重启kubelet

rm -rf /var/lib/kubelet/pki

systemctl start kubelet



## 11. 网络插件如果不能正常服务，重新生成网络插件的证书文件

mv /etc/calico/ /etc/calico.bak

ansible-playbook -i inventory.ini cluster.yml --tags=network





