

部署：

```
mkdir /etc/kubeasz/clusters/k8s-01/ -p
cd clusters/k8s-01/
cp ../../example/* ./
vim hosts.multi-node 


# 
[etcd]
192.168.1.1
192.168.1.2
192.168.1.3

# master node(s)
[kube_master]
192.168.1.1
192.168.1.2

# work node(s)
[kube_node]
192.168.1.3
192.168.1.4

# [optional] harbor server, a private docker registry
# 'NEW_INSTALL': 'true' to install a harbor server; 'false' to integrate with existed one

# 自己配置不用他安装
[harbor]
#192.168.1.8 NEW_INSTALL=false

# [optional] loadbalance for accessing k8s from outside
[ex_lb]
# 这里写自己haproxy的地址和VIP 的地址
192.168.1.6 LB_ROLE=backup EX_APISERVER_VIP=192.168.1.250 EX_APISERVER_PORT=8443
192.168.1.7 LB_ROLE=master EX_APISERVER_VIP=192.168.1.250 EX_APISERVER_PORT=8443

[all:vars]
# --------- Main Variables ---------------
# Secure port for apiservers
SECURE_PORT="6443"

# Cluster container-runtime supported: docker, containerd
CONTAINER_RUNTIME="docker"

# Network plugins supported: calico, flannel, kube-router, cilium, kube-ovn
CLUSTER_NETWORK="flannel"

# Service proxy mode of kube-proxy: 'iptables' or 'ipvs'
PROXY_MODE="ipvs"

# K8S Service CIDR, not overlap with node(host) networking
SERVICE_CIDR="10.68.0.0/16"

# Cluster CIDR (Pod CIDR), not overlap with node(host) networking
CLUSTER_CIDR="172.20.0.0/16"

# NodePort Range
NODE_PORT_RANGE="30000-32767"

# Cluster DNS Domain
CLUSTER_DNS_DOMAIN="cluster.local"	# 自己service的域名后缀，k8s创建的service都会带这个后缀

# -------- Additional Variables (don't change the default value right now) ---
# Binaries Directory
bin_dir="/opt/kube/bin"	# 命令安装路径

# Deploy Directory (kubeasz workspace)
base_dir="/etc/kubeasz"

# Directory for a specific cluster
cluster_dir="{{ base_dir }}/clusters/_cluster_name_"

# CA and other components cert/key Directory
ca_dir="/etc/kubernetes/ssl"

```







## 添加DNS服务

提供了多种dns服务，推荐使用coredns，新版本中kube-dns 已经不在支持

### kube-dns 

kube-dns 配置，由3个镜像实现

```
# 需要修改的地方

cluseterIP	# 进入pod看一下，dns默认执行的ip,改成哪个就行

limits	# 资源限制修改一下，默认的特别小，最少2G内存

--domain	# 当时部署时候service默认的后缀
# 所有的--PILLAR_DNS__DOMAIB__都换成自己的

image	# 镜像地址需要换成国内的或者自己私有的，一般有3个


# 将dns交给公司内部其他dns服务器解析
在dnsmasq下边找到
--server=/linxux.test/192.168.12.12#53	# 表示将linxux.test域名交给192.168.12.12的53端口解析
# 可以添加多个
```



### 添加coreDNS

https://github.com/coredns/coredns

k8s部署方式[deployment/kubernetes at master · coredns/deployment (github.com)](https://github.com/coredns/deployment/tree/master/kubernetes)这个部署方式需要使用脚本生成ymal配置文件，但是需要先使用kube-dns获取数据才能生成，不推荐。



k8s源码包内提供了coredns的部署yaml文件

https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.21.md#source-code-1

`\kubernetes\cluster\addons\dns\coredns`



```

# 修改域名地址
# corefile下边有个reday ，里边的域名改成自己的


# 内部域名转发,里边有个forward参数
forward . 自己的服务器ip

# 镜像地址改一下
imgage	# 改成本地的
```





DNS解释时，日过使用短域名默认只能解析自己namespace name内的service服务，日过跨namespace访问，需要使用完整的域名进行调用

==完整域名格式：server name.namespace name.svc.magedu.local==
dashboard-metrics-scraper.kubernetes-dashboard.svc.magedu.local





添加节点：





版本升级



二进制包下载：https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.21.md#node-binaries

升级程序有：

worknode：kubectl	kubelet	kube-proxy

masternode:kube-apiserver	kube-controller-manager kube-scheduler  kubectl	kubelet	kube-proxy







导入镜像

docker load -i 镜像名

修改tag

docker tag 镜像名 新的名字



批量替换

![image-20211217093337461](kubernetes集群部署.assets/image-20211217093337461.png)





