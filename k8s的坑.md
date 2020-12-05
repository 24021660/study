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




