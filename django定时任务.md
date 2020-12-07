定时任务使用的场景很多，以定时生成静态首页为例。
# 1. 安装支持包：django-crontab
安装方式：`pip install django-crontab`

# 2. 注册定时应用
在settings.INSTALLED_APPS中添加应用：
```
INSTALLED_APPS = [    
    'django_crontab', # 定时任务
]
```
# 3. 定时任务crontab的时间设置格式

定时时间基本格式 :  
5个'*'符号：* * * * * 命令  
依次对应：分 时 日 月 周  
```
M: 分钟（0-59）。每分钟用 * 或者 */1 表示
H：小时（0-23）。（0表示0点）
D：天（1-31）。
m: 月（1-12）。
d: 一星期内的天（0~6，0为星期天）。
```
示例：
```
每1分钟执行一次myCommand
* /1* * * * myCommand

每小时的第3和第15分钟执行
3,15 * * * * myCommand

在上午8点到11点的第3和第15分钟执行
3,15 8-11 * * * myCommand

每隔两天的上午8点到11点的第3和第15分钟执行
3,15 8-11 */2 * * myCommand

每周一上午8点到11点的第3和第15分钟执行
3,15 8-11 * * 1 myCommand
```
# 4. 设置定时任务
在settings中添加CRONJOBS，设置定时任务。

以下示例定时任务为：apps下--contents应用内--generate_static_index_html方法；
定时时间为：每隔一分钟运行一次；
">>"符：拼接定时任务运行结果保存路径。
```
例如我想进行定时执行test.py下的abc(),则为
CRONJOBS = [
    # 每1分钟生成一次首页静态文件
    ('*/1 * * * *', 'contents.crons.generate_static_index_html', '>> ' + os.path.join(os.path.dirname(BASE_DIR), 'logs/crontab.log'))
]
```
# 5. 设置中文支持
在定时任务中，如果出现非英文字符，会出现字符异常错误

在settings中添加：
CRONTAB_COMMAND_PREFIX = 'LANG_ALL=zh_cn.UTF-8'
# 6. 管理定时任务

添加定时任务到系统中,启动定时任务必须先执行改语句。
`$ python manage.py crontab add`

显示已激活的定时任务
`$ python manage.py crontab show`

移除定时任务
`$ python manage.py crontab remove`
