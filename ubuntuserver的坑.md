# 阿里云的源

# mongodb的坑：
一定要上官网上整安装步骤

# python跟django安装：
1.先安装`sudo apt-get install python3-pip`  
2.设置path路径：`sudo ln -s /usr/bin/python3 /usr/bin/python`  
3.然后安装django：`pip3 install django`  
4.安装python-common：`sudo install python-django-common`  
5.然后安装django命令组件：`sudo apt-get install python-django`  

# 安装uwsgi：pip install uwsgi
依赖包：apt-get install build-essential python-dev
uwsgi动态处理能力较强：所以目前可以先用uwsgi带项目：
## 1.配置文件编写：
  进入/script目录（manage.py上级目录），创建一个`uwsgi.ini`文件
### uwsig使用配置文件启动
```
[uwsgi]
http-socket = :8080
chdir=/home/tony/kaigeda #这里一定要指定manage.py的路径否则noapp
virtualenv = /root/.virtualenvs  #这里如果有虚拟环境的话需要配置虚拟环境
wsgi-file = /kaida/wsgi.py #也可以写成module=test
processes = 4
master = true
#daemonize = ./log/uwsgi.log
pidfile = uwsgi.pid
#vacuum = true
#log-maxsize = 50000000
threads = 2
plugin=python3
```
### 安装包：
`sudo apt install uwsgi-plugin-python3`
### 运行：
 `uwsgi --http-socket 0.0.0.0:8080 --plugin python3 --chdir /home/tony/HelloWorld --file /HelloWorld/wsgi.py --processes 4 --threads 2`
进程过多：`fuser -k 9090/tcp`
停止uwsgi：`uwsgi --stop uwsgi.pid`
杀死所有进程：`killall -s INT /usr/local/bin/uwsgi`



# 安装ngnix：
`sudo apt-get install ngnix`

在文件夹`/etc/nginx/conf.d/test.conf`

```
server
    {
        listen  8080;              #配置监听端口和主机名称
        server_name 0.0.0.0；

        access_log  /myweb/server1/logaccess.log;#配置请求处理日志存放路径
        error_page 404  /404.html;     #配置错误页面

        location  /server1/location1 { #配置处理／server1/location1 请求的location
            include     /etc/nginx/uwsgi_params;
        }

        location  /server1/location2 { #配置处理／server1/location2 请求的location
            root  /myweb;
            index index.svr1-loc2.htm;
        }
    }
```
配置：
启动：`sudo /etc/init.d/nginx start`

# 一些django在ubuntu启动必备库：
跨域：`pip3 install django-cors-headers`
mongo数据库：`pip3 install pymongo`
`pip3 install qcloudsms_py`

## supervisor进程守护：
1.安装`pip install supervisor`  
2.打开内容并复制里面的内容：echo_supervisord_conf  
3.编写新文件：sudo vim /etc/supervisord.conf，将之前的2中内容复制进来  
4.将  
`;[include]  
;files = relative/directory/*.ini`  
改为：  
`[include]  
files = /etc/supervisor/*.conf`  
并去掉前面注释  
5.新建文件夹：  
`cd /etc`  
`mkdir supervisor`  
6.新建文件  
`sudo vim /etc/supervisor/uwsgi.conf`  
复制内容  
```
[program:uwsgi]
command=/usr/local/bin/uwsgi --ini /home/mysite_uwsgi/mysite.ini
user=root
autorestart=true
autostart=true
startretries=3
redirect_stderr=true
startsecs=5
stdout_logfile=/var/log/django/supervisor.log
stopasgroup=true
killasgroup=true
priority=999
```
7.干掉所有uwgsi进程`ps -aux | grep uwsgi |awk '{print $2}'|xargs kill -9`  
8.启动supervisor：`supervisord -c /etc/supervisord.conf`  
9.查看状态：`supervisorctl status`  
干掉supervisor：`unlink /tmp/supervisor.sock`  
# 负载均衡：
首先保证uwsgi是开启的，
在负载均衡器中添加`fuzai.conf`
```
upstream myapp1 {
                server 192.168.245.139:8080 weight=10;
                server 172.17.8.93:8080 weight=10;
                server 192.168.245.144:8080 weight=10;
                }

        server {
                listen 80;
                server_name 192.168.245.139;
                location / {
                        proxy_pass http://myapp1;
                        include proxy_params;
                }
```
同时相应的的服务器`/etc/nginx/conf.d`中添加
```
server
  {
    listen 80;#监听端口
    server_name localhost;#域名
    }
```
    
# 改固定ip
进入文件夹：`cd /etc/netplan`
然后进行修改：`sudo vim *.yaml`
```conf
# Let NetworkManager manage all devices on this system
network:
  version: 2
  # renderer: NetworkManager
  ethernets:
          ens33:
                  addresses: [192.168.0.111/24]
                  gateway4: 192.168.0.1
                  nameservers:
                        addresses: [192.168.0.1]
```
然后保存之后，输入`sudo netplan apply`即可

# 解决nginx更新文件不更新的问题
在nginx.conf中进行设置，sendfile off;
然后文件全部删除重新复制一份

打开终端输入–>sudo timedatectl set-local-rtc 1     ->回车，重启电脑！
# ftp服务器搭建
安装ftp服务器：`sudo apt-get install vsftpd`
设置目录与用户权限`sudo useradd -d /home/ftp -s /bin/bash ftpname`
其中ftpname为用户名，为新建用户设置密码：`passwd ftpname`
# 查询端口占用
`netstat -ap | grep 8080`
`lsof -i`