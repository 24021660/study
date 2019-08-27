# study
begin to learn new language and how to use Github
1.mongodb安装：
先下载压缩包，找好位置解压之后，mkdir -p /data/db
export PATH=<mongodb-install-directory>/bin:$PATH
然后运行sudo ./mongod  -dbpath /data/db/
之后看看mongo能否启动，如果能启动，则可以进行shell
2.如果启动出现错误，一般为缺少curl：
    sudo apt-get install curl
    sudo apt-get install libcurl3
  之后再运行应该没问题了。
3.远程链接出现访问积极拒绝，需要进入bin中的cfg或cof文件中，将bindip改成0.0.0.0，并且将注释去掉，关键在于mongod.conf文件中的ip，完了之后需要sudo service mongod restart。
4.django中session的问题：首先看看settings里面middleware=有没有'django.contrib.sessions.middleware.SessionMiddleware'
