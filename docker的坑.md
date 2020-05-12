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


