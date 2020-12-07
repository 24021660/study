测试运行，直接python3 run.py   
uwsgi:   
先写配置文件   
然后运行就可以了  
```ini
[uwsgi]
http = 0.0.0.0:8040  
chdir=/home/aiops_project/
wsgi-file = /home/aiops_project/run.py 
callable=app 
processes = 1
threads = 10
pidfile = aiops_flask.pid
demonize = /home/aiops_project/serve.log
```

