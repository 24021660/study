## 1.安装
```shell
echo "deb https://downloads.apache.org/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list

curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -

sudo apt-get update

//如果出现错误,则
sudo apt-key adv --keyserver pool.sks-keyservers.net --recv-key A278B781FE4B2BDA

sudo apt-get install cassandra
```

## 2.启动
启动 `Cassandra sudo service cassandra start`  
停止 `sudo service cassandra stop` 通常该服务将自动启动。  
因此，如果需要进行任何配置更改，请确保将其停止。  
通过`nodetool status`从命令行调用来验证Cassandra是否正在运行。  
配置文件的默认位置是`/etc/cassandra`。  
日志和数据目录的默认位置是`/var/log/cassandra/`和`/var/lib/cassandra`。  
可以在中配置启动选项（堆大小等）`/etc/default/cassandra`。

## 3.python中使用
连接数据库：
```python
from cassandra.cluster import Cluster #引入Cluster模块
cluster = Cluster() #连接本地数据库，如果是本地地址，写不写都可以
cluster = Cluster(['127.0.0.1'])#连接本地数据库
session = cluster.connect()#简单的连接
session = cluster.connect('keyspacename')#指定连接keyspace，相当于sql中use dbname
session.set_keyspace('otherkeyspace') #设置、修改keyspace
session.execute('use keyspacename')#设置、修改keyspace
```
查询：
```python
rows = session.execute('select * from emp')
for row in rows:#遍历查询的结果
    print(str(row[0])+row[1]+row[2]+row[3]+str(row[4])) #如果你的row[0] 不是varchar 或者text类型，需要转一下类型，不然python会报错
for (emp_id,emp_city,emp_email,emp_name,emp_phone) in rows:#也可以用这种方式遍历查询的结果
    print(str(emp_id)+emp_city+emp_email+emp_name+str(emp_phone))
session.execute(
"""
INSERT INTO emp (emp_id,emp_city,emp_email,emp_name,emp_phone)
VALUES (%s, %s, %s, %s, %s)
""",
(4, 'tianjin', '156.com','pon',145645)
)
session.execute("INSERT INTO emp (emp_id) VALUES (%s)", (5,)) #如果只传一个参数，用tuple的形式必须后面加“，”，或者用list的形式
session.execute("INSERT INTO emp (emp_id) VALUES (%s)", [6]) 
session.execute(
"""
INSERT INTO emp (emp_id,emp_city,emp_email,emp_name,emp_phone)
VALUES (%(emp_id)s, %(emp_city)s, %(emp_email)s, %(emp_name)s, %(emp_phone)s)
""",{'emp_id': 7, 'emp_city': 'xian', 'emp_email': '777777.qq.com', 'emp_name': 'xiaoming', 'emp_phone': 55555})
cluster.shutdown()
cluster.is_shutdown #查看是否关闭连接

tic = time.time()
i=0
sql = 'BEGIN BATCH\n'
with open(r'C:\Users\admin\Desktop\output\cassandra116w.csv', 'r') as f:
    while True:
        line = f.readline().strip()
        if (line == '' or line == np.nan): 
            if(sql != 'BEGIN BATCH\n'):
                sql += 'APPLY BATCH;'
                session.execute(sql)
            break
        ll = line.split(',')
        sql += 'INSERT INTO lead2(name,current_title,current_company,location,id) VALUES (' + '\''+ll[0]+'\'' + ','+'\''+ll[1]+'\''+',' +'\''+ ll[2] +'\''+  ',' +'\''+ ll[3]+'\'' +',' +'\''+ ll[4] +'\''+');\n'
        i=i+1
        if (i>300):
            sql += 'APPLY BATCH;'
            session.execute(sql)
            i=0
            sql = 'BEGIN BATCH\n'
toc = time.time()
print('vectorized version:' +str((toc - tic))  +'s')  
vectorized version:116.4514513015747s  #插入116万条数据，用时116秒
```

