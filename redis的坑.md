# study
begin to learn new language and how to use Github
# 进军的redis
## 1.Ubuntu 安装redis:
  ```
  $sudo apt-get update 升级原有包  
  $sudo apt-get install redis-server 安装  
  $ redis-server 运行  
  $ redis-cli 测试  
  redis 127.0.0.1:6379>
  redis 127.0.0.1:6379> ping
  PONG
  ```
## 2.windows下安装redis:
  下载压缩包,解压之后,
  ```
  redis-server.exe redis.windows.conf 运行服务器端
  redis-cli.exe -h 127.0.0.1 -p 6379 运行客户端
```
## 3.远程连接:
(1)解绑:在conf中将本机地址解绑,即讲127.0.0.1注释掉,然后找到#requirepass 123456789 #假设123456789是我的redis密码,把注释消掉
然后重启服务器   
(2)`$ redis-cli -h host -p port -a password `远程链接服务器就行了
`redis-cli -h 192.168.245.159 -p 6379 -a "mypass"`
停止redis
`redis-cli shutdown`
## 4.django中使用redis
 (1)`pip install django-redis`
 settings里面:
 ```python
 CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://your_host_ip:6379/1',
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
             "PASSWORD": "yoursecret",
        },
    },
}
SESSION_ENGINE = "django.contrib.sessions.backends.cache"
SESSION_CACHE_ALIAS = "default"
```
(2)测试:
  ```
  python manage.py shell
  from django.core.cache import cache #引入缓存模块
  cache.set('v', '555', 60*60)      #写入key为v，值为555的缓存，有效期30分钟
  cache.has_key('v') #判断key为v是否存在
  cache.get('v')     #获取key为v的缓存
```

