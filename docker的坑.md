## 1.docker安装
卸载旧版本
Docker 的旧版本被称为 docker，docker.io 或 docker-engine 。如果已安装，请卸载它们：

$ sudo apt-get remove docker docker-engine docker.io containerd runc
当前称为 Docker Engine-Community 软件包 docker-ce 。

安装 Docker Engine-Community，以下介绍两种方式。

使用 Docker 仓库进行安装
在新主机上首次安装 Docker Engine-Community 之前，需要设置 Docker 仓库。之后，您可以从仓库安装和更新 Docker 。

设置仓库
更新 apt 包索引。

$ sudo apt-get update
安装 apt 依赖包，用于通过HTTPS来获取仓库:

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
添加 Docker 的官方 GPG 密钥：

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88 通过搜索指纹的后8个字符，验证您现在是否拥有带有指纹的密钥。

$ sudo apt-key fingerprint 0EBFCD88
   
pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
使用以下指令设置稳定版仓库

$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) \
  stable"
安装 Docker Engine-Community
更新 apt 包索引。

$ sudo apt-get update
安装最新版本的 Docker Engine-Community 和 containerd ，或者转到下一步安装特定版本：

$ sudo apt-get install docker-ce docker-ce-cli containerd.io
要安装特定版本的 Docker Engine-Community，请在仓库中列出可用版本，然后选择一种安装。列出您的仓库中可用的版本：

$ apt-cache madison docker-ce

  docker-ce | 5:18.09.1~3-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
  docker-ce | 5:18.09.0~3-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
  docker-ce | 18.06.1~ce~3-0~ubuntu       | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
  docker-ce | 18.06.0~ce~3-0~ubuntu       | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
  ...
使用第二列中的版本字符串安装特定版本，例如 5:18.09.1~3-0~ubuntu-xenial。

$ sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io
测试 Docker 是否安装成功，输入以下指令，打印出以下信息则安装成功:

$ sudo docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete                                                                                                                                  Digest: sha256:c3b4ada4687bbaa170745b3e4dd8ac3f194ca95b2d0518b417fb47e5879d9b5f
Status: Downloaded newer image for hello-world:latest


Hello from Docker!
This message shows that your installation appears to be working correctly.


To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.


To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash


Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/


For more examples and ideas, visit:
 https://docs.docker.com/get-started/
## 2.docker加速
1. 安装／升级Docker客户端
推荐安装1.10.0以上版本的Docker客户端，参考文档 docker-ce
2. 配置镜像加速器
针对Docker客户端版本大于 1.10.0 的用户
您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器
ubuntu:
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://tdl7id8q.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
mac:
```
针对安装了Docker for Mac的用户，您可以参考以下配置步骤：

在任务栏点击 Docker Desktop 应用图标 -> Perferences，在左侧导航菜单选择 Docker Engine，在右侧输入栏编辑 json 文件。将

https://c9eegqj0.mirror.aliyuncs.com加到"registry-mirrors"的数组里，点击 Apply & Restart按钮，等待Docker重启并应用配置的镜像加速器。

```
## 3.docker封装flask
创建应用
首先，编写一个简单的Flask应用：docker_test/flask_app.py
Docker简介
Docker 镜像
Docker镜像 (Image)类似于虚拟机镜像，可以将它理解为一个面向Docker引擎的只读模版，包含了文件系统。镜像是创建Docker容器的基础。
Docker 容器
Docker容器 (Container)类似于一个轻量级的沙箱，Docker利用容器来运行和隔离应用。容器是从镜像创建的应用运行实例，可以将其启动、开始、停止、删除，而这些容器都是相互隔离、互不相见的。
Docker 仓库
Docker仓库 (Repositort)类似于代码仓库，是Docker集中存放镜像文件的场所。根据公开分享与否，Docker仓库分为公开仓库 (Public)和私有仓库 (Private)两种形式。
目前最大的公开仓库是Docker Hub，存放了大量的镜像供用户下载。
Docker 部署步骤
下载python镜像

可在docker.hub网站上查询python镜像的版本，下载想要的版本
docker pull python:XXX

运行python镜像

docker run -it python:XXX

另起一个控制台窗口，查看上步容器的ID

docker ps|grep python 可以看到当前容器的ID

进入python容器

docker exec -it 容器ID /bin/bash

安装Flask应用所需的依赖包

pip install -r requirements.txt

安装完成后，另起一个窗口，将当前的容器制作成镜像

docker commit 容器ID 新名字

在docker_test/下编写dockfile, 举例

FROM docker_test:v0.1
COPY flask_app.py /flask_app.py
EXPOSE 5000
ENTRYPOINT ["python","flask_app.py"]

第一行为基础镜像，即第7步中制作的镜像
第二行是将Python程序导入容器
第三行是暴露python的端口
第四行是运行容器时执行的命令

在docker_test/下制作镜像

docker build -t 镜像名字 .

运行容器

docker run -p5000:5000 镜像名字

## 4.docker操作
`docker container ls -all` 查看所有容器
docker导入导出
docker save 名字 -o 镜像名字>输出目录
sudo docker save jenkins:aiops>/home/jenkinsaiops.tar
取得root权限并进入：
sudo docker exec -ti -u root 7509371edd48 bash
docker rmi `docker image -q`   #删除所有的镜像
## 5.修改运行中的docker端口
在docker run创建并运行容器的时候，可以通过-p指定端口映射规则。但是，我们经常会遇到刚开始忘记设置端口映射或者设置错了需要修改。当docker start运行容器后并没有提供一个-p选项或设置，让你修改指定端口映射规则。那么这种情况我们该怎么处理呢？今天Docker君教你如何修改运行中的docker容器的端口映射？

方法一：删除原有容器，重新建新容器
这个解决方案最为简单，把原来的容器删掉，重新建一个。当然这次不要忘记加上端口映射。



    优缺点：优点是简单快捷，在测试环境使用较多。缺点是如果是数据库镜像，那重新建一个又要重新配置一次，就比较麻烦了。

方法二：修改容器配置文件，重启docker服务
容器的配置文件路径：

/var/lib/docker/containers/[hash_of_the_container]/hostconfig.json

    其中的hashofthecontainer是docker镜像的hash值，可以通过docker ps或者docker inspect containername查看。（CONTAINER ID就可以看出来）



 



    如上图，文件中其中有一项是PortBindings，其中8080/tcp对应的是容器内部的8080端口，HostPort对应的是映射到宿主机的端口9190。8361/tcp对应的是容器内部的8361端口，HostPort对应的是映射到宿主机的端口9191。按需修改端口，然后重启docker服务，再启动容器服务就可以了。

systemctl restart docker

    优缺点：这个方法的优点是没有副作用，操作简单。缺点是需要重启整个docker服务，如果在同一个宿主机上运行着多个容器服务的话，就会影响其他容器服务。

 

方法三：利用docker commit新构镜像
    docker commit：把一个容器的文件改动和配置信息commit到一个新的镜像。这个在测试的时候会非常有用，把容器所有的文件改动和配置信息导入成一个新的docker镜像，然后用这个新的镜像重起一个容器，这对之前的容器不会有任何影响。

1、停止docker容器

docker stop container01

2、commit该docker容器

docker commit container01 new_image:tag

3、用前一步新生成的镜像重新起一个容器

docker run --name container02 -p 80:80 new_image:tag

    优缺点：这种方式的优点是不会影响统一宿主机上的其他容器，缺点是管理起来显得比较乱，没有第二种方法那么直观。

## 6.docker安装与启动各种软件
### 1.cassandra
docker pull cassandra
然后运行：
docker run --name some-cassandra -v /my/own/datadir:/var/lib/cassandra -d cassandra:lastest

## 7.docker集群管理
### 使用portainer
#### 每个节点开放2375端口：
打开文件
```
vi /lib/systemd/system/docker.service
```
在ExecStart加入以下参数
```
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock --containerd=/run/containerd/containerd.sock
```
然后重启docker：
```
systemctl daemon-reload // 1，加载docker守护线程
systemctl restart docker // 2，重启docker
```
#### 安装portainer
```
docker run -d -p 9000:9000 \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
--name prtainer-test \
portainer/portainer
```
然后登陆之后，用户名：admin，密码：可以自己设定
然后选择remote，url填写IP地址+2375端口就可以了
#### docker sudo组
sudo gpasswd -a ${USER} docker
sudo service docker restart 

#### 访问docker仓库
```
sudo echo '{ "insecure-registries":["10.1.63.217:5000"] }' > /etc/docker/daemon.json
systemctl restart docker
```
#### docker空间不足需要迁移到其他盘符：
1.docker info 查看docker的初始位置
2.将/var/lib/docker文件夹复制到相应位置
3.然后修改/lib/systemd/system/docker.service,将

#### docker 安装oracle
oracle官方下载：进入oracle官网进行下载
git上有相关的dockerfile：https://github.com/oracle/docker-images
下载下来之后，找到oracledatabase，进入其中的singleinstance/dockerfiles,修改执行权限：`chmod a+x *.sh && chmod a+x */*.sh`
然后将官方对应版本的zip拷贝进去，

## docker管理权限