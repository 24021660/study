# 1.安装
ubuntu：
```shell
sudo su
apt-get update && apt-get install -y apt-transport-https
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
sudo cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
```
centos:
```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
setenforce 0
yum install -y kubelet kubeadm kubectl
systemctl enable kubelet && systemctl start kubelet
```
2.配置
配置文件：
```
~/.kube/config
#检查是否配置，以及配置状态
kubectl cluster-info
#如果配置了但进不去，则使用
kubectl cluster-info dump
```
3.准备：
```
docker驱动改为systemd
编辑 /etc/docker/daemon.json (没有该文件就新建一个），添加如下启动项参数即可：
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}



swap分区关闭：swapoff -a
#永久关闭，修改/etc/fstab,注释掉swap一行


防火墙关闭：

版本一致：
```
然后看下需要下载的镜像：

```
kubeadm config images list
#k8s.gcr.io/kube-apiserver:v1.19.4
#k8s.gcr.io/kube-controller-manager:v1.19.4
#k8s.gcr.io/kube-scheduler:v1.19.4
#k8s.gcr.io/kube-proxy:v1.19.4
#k8s.gcr.io/pause:3.2
#k8s.gcr.io/etcd:3.4.13-0
#k8s.gcr.io/coredns:1.7.0

然后docker pull：
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.19.4
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager:v1.19.4
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler:v1.19.4
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.19.4
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/etcd:3.4.13-0
docker pull coredns/coredns:1.7.0

然后转tag：
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver:v1.19.4 k8s.gcr.io/kube-apiserver:v1.19.4
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager:v1.19.4 k8s.gcr.io/kube-controller-manager:v1.19.4
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler:v1.19.4 k8s.gcr.io/kube-scheduler:v1.19.4
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy:v1.19.4 k8s.gcr.io/kube-proxy:v1.19.4
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2 k8s.gcr.io/pause:3.2
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/etcd:3.4.13-0 k8s.gcr.io/etcd:3.4.13-0
docker tag coredns/coredns:1.7.0 k8s.gcr.io/coredns:1.7.0
```
然后初始化kadmin


# k8s集群安装：
每个节点的操作：
(1)修改文件
```sh
#修改/etc/hosts
192.168.50.128 k8s-master01
192.168.50.129 k8s-master02
192.168.50.130 k8s-master03
192.168.50.131 k8s-node01
192.168.50.132 k8s-node02
#修改/etc/sysconfig/selinux
SELINUX=disabled
SELINUXTYPE=targeted
#注释挂载项/etc/fstab
#UUID=afb91fab-xxxxxxxxxxx swap         swap  defaults 0 0

```
（2）操作
```sh
#关闭防火墙
systemctl disable --now firewalld
systemctl disable --now dnsmasq
systemctl disable --now NetworkManager
#关闭Selinux
setenforce 0
#关闭swap分区
swapoff -a && sysctl -w vm.swappiness=0
#统一时区
yum install ntpdate -y
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
echo 'Asia/Shanghai' >/etc/timezone
ntpdate time2.aliyun.com
#所有节点配资后limit
ulimit -SHn 65535
```
只在master01上操作
```sh
#免密登陆
ssh-keygen -t rsa
for i in k8s-master-01 k8s-master-02 k8s-master-03 k8s-node-01 k8s-node-02; do ssh-copy-id -i .ssh/id_rsa.pub $i;done

#换源
for i in k8s-master-01 k8s-master-02 k8s-master-03 k8s-node-01 k8s-node-02;
do scp -r /home/res/ $i:/opt;done
```
每台机器换源操作
```sh
cd /etc/yum.repos.d
mkdir bak
mv *.repo bak/
mv /opt/* .
ds
yum install wget jq psmisc vim net-tools -y
yum update -y --exclude=kernel* && reboot

yum install ipvsadm ipset sysstat conntrack libseccomp  -y
```
# 2.基本组件安装
```sh
#安装docker
yum list docker-ce.x86_64 --showduplicates | sort -r
yum -y install docker-ce-17.09.1.ce-1.e17.centos
#安装k8s组件
yum list kubeadm.x86_64 --showduplicates |sort -r 
yum install -y kubeadm-1.12.3-0.x86_64 kubectl-1.12.3-0.x86_64 kubelet-1.12.3-0.x86_64
systemctl enable --now docker
DOCKER_CGGROUPS=${docker info | grep 'Cgroup' | cut -d' ' -f3}
cat >/etc/sysconfig/kubelet<<EOF
KUBELET_EXTRA_ARGS="--CGROUP-DRIVER=$DOCKER_CGROUPS --pod-infra-container-image=registry.cn-hangzhou.aliyuncs.com/google_containers/pause-amd64:3.1"
EOF
#kubelet开机自启动
systemctl daemon-reload
systemctl enable --now kubelet

#安装高可用haproxy+keepalived
yum install keepalived haproxy -y
```
所有master节点配置haproxy
```sh
mkdir /etc/haproxy
vi /etc/haproxy/haproxy.cfg
#内容为

```
所有master节点配置keepalived
```sh
#master01

#master02

#健康检查需要等集群建立完成后再开启

```
启动haproxy和keepalived
```sh
systemctl enable --now haproxy
systemctl enable --now keepalived
```






