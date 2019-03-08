# study
begin to learn new language(python) and how to use Github
数据库连接：
sqlserver请安装：pip install django-pyodbc-azure

DATABASES = {
    'default': {
        #'ENGINE': 'django.db.backends.sqlite3',
        #'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        'ENGINE': 'sql_server.pyodbc',
        'NAME': 'MyDjango',#数据库名
        'USER': 'sa',#数据库登录用户
        'PASSWORD': '123456',#数据库密码
        'HOST': '127.0.0.1',#数据库服务器
        'PORT': '',#端口，默认1433
        'OPTIONS': {#odbc驱动
            'driver': 'SQL Server Native Client 11.0',
            'MARS_Connection': True,
        },
    }
 }
 
 连接已经有数据表数据库
 新建app模型：django-admin startapp 模型名称
 然后：python manage.py inspectdb 
 运行完毕之后：python manage.py inspectdb >TestModel/models2.py（你的模型文件夹/模型文件
