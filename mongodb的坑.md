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
mongodb在ubuntu中的安装方法：
    1.先看是否是最新版，最新版的版本号
    2.wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -
    3.echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list
    4.sudo apt-get update
    5.sudo apt-get install -y mongodb-org
    6.echo "mongodb-org hold" | sudo dpkg --set-selections
echo "mongodb-org-server hold" | sudo dpkg --set-selections
echo "mongodb-org-shell hold" | sudo dpkg --set-selections
echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
echo "mongodb-org-tools hold" | sudo dpkg --set-selections
    7.sudo service mongod start
    8.修改mongd.conf中的bindip为0.0.0.0
    9.重启mongod
