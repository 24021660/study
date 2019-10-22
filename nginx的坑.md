# 配置从三个方面着手
### 1.系统层面配置
### 2.nginx配置
### 3.uwsgi配置

## 一：系统层面配置
### 1、调整同时打开文件数量
```shell
ulimit -n 20480
ulimit -a  # 查看是否生效
```
### 2、TCP最大连接数（somaxconn）
```
echo 10000 > /proc/sys/net/core/somaxconn
```
### 3、TCP连接立即回收、回用（recycle、reuse）
```
echo 1 > /proc/sys/net/ipv4/tcp_tw_reuse
echo 1 > /proc/sys/net/ipv4/tcp_tw_recycle
```
### 4、不做TCP洪水抵御
```
echo 0 > /proc/sys/net/ipv4/tcp_syncookies
```
也可以直接使用优化后的配置，在/etc/sysctl.conf中加入：
```conf
net.core.somaxconn = 20480  # 定义了系统中每一个端口最大的监听队列的长度，这是个全局的参数。默认是128.优化可以根据系统配置做优化
net.core.rmem_default = 262144
net.core.wmem_default = 262144
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.ipv4.tcp_rmem = 4096 4096 16777216
net.ipv4.tcp_wmem = 4096 4096 16777216
net.ipv4.tcp_mem = 786432 2097152 3145728
net.ipv4.tcp_max_syn_backlog = 16384
net.core.netdev_max_backlog = 20000
net.ipv4.tcp_fin_timeout = 15
net.ipv4.tcp_max_syn_backlog = 16384
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_max_orphans = 131072
net.ipv4.tcp_syncookies = 0
```
使用：sysctl -p 生效
```shell
sysctl -p
```
## 二：nginx配置
nginx 是一个高效的服务器，但是如果只是安装，没有做什么配置的话，那么它最多也就只能承受1000个左右的并发，如何可以让nginx能轻松应对5000甚至10000的高并发呢，修改nginx配置文件，nginx.conf：
增加`work_rlimit_nofile`和`worker_connections`数量，并禁用`keepalive_timeout`。
```conf
...
worker_processes  1; #nginx 进程数，建议按照cpu 数目来指定，一般为它的倍数
worker_rlimit_nofile 20000; #一个nginx 进程打开的最多文件描述符数目，理论值应该是最多打开文件数（ulimit -n）与nginx 进程数相除，但是nginx 分配请求并不是那么均匀，所以最好与ulimit -n 的值保持一致

events {
    use epoll;#使用epoll的I/O模型
    worker_connections 20000;#每个进程允许的最多连接数， 理论上每台nginx 服务器的最大连接数为worker_processes*worker_connections
    multi_accept on;
}

http {
    keepalive_timeout 0;
}
...
```
### 三：uwsgi配置
```conf
workers/processes = 24   # 并发处理进程数
listen = 65535  # 并发的socket 连接数。默认为100。优化需要根据系统配置
```
### 四：测试
```shell
ab -r -n 10000 -c 5000 -H "User-Agent: python-keystoneclient" 
-H "Accept: application/json" -H "X-Auth-Token: 65e194"  http://172.16.29.10:81/
```


# nginx负载均衡
## 1.nginx反向代理
`proxy_pass`:用来设置被代理服务器的地址。
使用方法：
```nginx
upstream abc
{
    server 192.168.1.2:8080;
    server 192.168.1.3:8080;
    server 192.168.1.4:8080;
}
server
{
    listen 80;
    server_name www.myweb.com; 
    location / {
       proxy_pass http://abc;
    }
}
```
`proxt_set_header`:用于更改nginx服务器接收到的客户端请求的请求头信息。一般使用在proxy_pass下面。
## 2.nginx负载均衡操作步骤
目前有三台机器，IP地址分别为：`192.168.245.139`,`192.168.245.146`,`192.168.245.147`
```nginx
upstream backend
{
    server 192.168.1.91:8080  weight=5;
    server 192.168.245.146:8080  weight=2;
    server 192.168.245.147:8080;
}
server 
{
 listen 8090;
 server_name www.myweb.com;
 index index.html index.htm;
 location / {
     proxy_pass http://backend;
     proxy_set_header Host $host;
 }
}
```
首先，我们使用django框架，做出三个网页，分别为test1，test2，test3
，然后复制到相应机器上
运行`nginx`，或者`nginx -t`查看语法是否有误
修改/etc/nginx/nginx.conf中http下的内容，将上面的内容复制进去，即可实现简单的负载均衡。

