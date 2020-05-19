# 1.docker安装jenkins最新版本
1.pull一个jenkins镜像 `docker pull jenkins/jenkins:lts`;
这个是安装最新版的jenkins,如果安装旧版本，很多插件安装不上，docker环境下升级又比较麻烦。

2.查看已经安装的jenkins镜像 `docker images`;
查看是否是最新版 docker inspect ba607c18aeb7

3.创建一个jenkins目录 `mkdir /home/jenkins_home`;

4.启动一个jenkins容器 `docker run -d --name jenkins_01 -p 8081:8080 -v /home/jenkins_01:/home/jenkins_01 jenkins/jenkins:lts`;

5.查看jenkins服务 `docker ps | gdockerrep jenkins`;

6.启动服务端 。`localhost:8081`;

7.进入容器内部`docker exec -it jenkins_01 bash`；

8.执行：`cat /var/jenkins_home/secrets/initialAdminPassword`，得到密码并粘贴过去

9.输入密码之后，重启docker镜像 `docker restart {CONTAINER ID}`，安装完毕。

# 2.gitlab+jenkens配置





