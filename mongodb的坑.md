# study
begin to learn new language and how to use Github
# 1.mongodb安装：
获取:`wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -`  
`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list`  
升级系统:`sudo apt-get update`  
安装:`sudo apt-get install -y mongodb-org `
```
echo "mongodb-org hold" | sudo dpkg --set-selections
echo "mongodb-org-server hold" | sudo dpkg --set-selections
echo "mongodb-org-shell hold" | sudo dpkg --set-selections
echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
echo "mongodb-org-tools hold" | sudo dpkg --set-selections
```
# 2.mongodb启动与暂停
启动:`sudo service mongod start`  
暂停:`sudo service mongod stop`
# 3.远程链接出现访问积极拒绝
需要进入bin中的cfg或cof文件中，将bindip改成0.0.0.0，并且将注释去掉，关键在于mongod.conf文件中的ip，完了之后需要sudo service mongod restart。   
# 4.mongodb备份与恢复：
## 1.备份:
`mongodump -h dbhost -d dbname -o dbdirectory`
`-h`：
MongDB所在服务器地址，例如：127.0.0.1，当然也可以指定端口号：127.0.0.1:27017

`-d`：
需要备份的数据库实例，例如：test

`-o`：
备份的数据存放位置，例如：c:\data\dump，当然该目录需要提前建立，在备份完成后，系统自动在dump目录下建立一个test目录，这个目录里面存放该数据库实例的备份数据。

## 2.恢复:
`mongorestore -h <hostname><:port> -d dbname <path>`
    --host <:port>, -h <:port>：
MongoDB所在服务器地址，默认为： localhost:27017

--db , -d ：
需要恢复的数据库实例，例如：test，当然这个名称也可以和备份时候的不一样，比如test2

--drop：
恢复的时候，先删除当前数据，然后恢复备份的数据。就是说，恢复后，备份后添加修改的数据都会被删除，慎用哦！

<path>：
mongorestore 最后的一个参数，设置备份数据所在位置，例如：c:\data\dump\test。

你不能同时指定 <path> 和 --dir 选项，--dir也可以设置备份目录。

--dir：
指定备份的目录

你不能同时指定 <path> 和 --dir 选项。


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
## 3.docker安装mongodb
### 1.拉取镜像
这里我们拉取官方的最新版本的镜像：
```
$ docker pull mongo:latest
```
### 2、查看本地镜像
使用以下命令来查看是否已安装了 mongo：
```
$ docker images
```
在上图中可以看到我们已经安装了最新版本（latest）的 mongo 镜像。
### 3、运行容器
安装完成后，我们可以使用以下命令来运行 mongo 容器：
```docker
$ docker run -itd --name mongo -p 27017:27017 mongo --auth
```
参数说明：
```
-p 27017:27017 ：映射容器服务的 27017 端口到宿主机的 27017 端口。外部可以直接通过 宿主机 ip:27017 访问到 mongo 的服务。
--auth：需要密码才能访问容器服务。
```
### 4、安装成功
最后我们可以通过 docker ps 命令查看容器的运行信息：
接着使用以下命令添加用户和设置密码，并且尝试连接。
```docker
$ docker exec -it mongo mongo admin
# 创建一个名为 admin，密码为 123456 的用户。
>  db.createUser({ user:'admin',pwd:'123456',roles:[ { role:'root', db: 'admin'}]});
# 尝试使用上面创建的用户信息进行连接。
> db.auth('admin', '123456')
```
## 链表查询
表字段定义
mycol_case_collection = mydb["case_collection"]   #表A
mycol_task_collection = mydb["task_collection"]   #表B
A:
mylist_case_collection = [
    {"user_id": "yu", "case_id": "1", "case_name": "案件名1","task_id": ["10000", "10001"]}
]
B:
mylist_task_collection = [
    {"task_id": "10000","task_type": "1", "task_name": "任务名1-1","create_time": "2019-8-26 15:30:22"}
]
对于两张表中的task_id字段进行联合查询
————————————————
版权声明：本文为CSDN博主「yuer_xiao」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/yuer_xiao/article/details/100164847
mycol_case_collection = mydb["case_collection"]
 
#当前表A
result = mycol_case_collection.aggregate(
    [
        {
            '$lookup':
                {
                    "from": "task_collection", #需要联合查询的另一张表B
                    "localField": "task_id",   #表A的字段
                    "foreignField": "task_id", #表B的字段
                    "as": "task_docs"          #根据A、B联合生成的新字段名
                },
        },
        {
            '$project':                        #联合查询后需要显示哪些字段，1：显示
                {
                    'task_docs.task_id':1,
                    'task_docs.task_name':1,
                    'task_docs.task_type':1,
                    'task_docs.evidence_content':1,
                    'case_id':1,
                    'task_id':1,
                    'user_id':1,
                    '_id':0,
                },
         },
        {
            '$match':                           #根据哪些条件进行查询
                {
                    "user_id" : name            #这里是根据表A中 user_id == name 
                }
        }
    ]
)
# 高级查询
符号	含义	示例
$lt	小于	{'age': {'$lt': 20}}
$gt	大于	{'age': {'$gt': 20}}
$lte	小于等于	{'age': {'$lte': 20}}
$gte	大于等于	{'age': {'$gte': 20}}
$ne	不等于	{'age': {'$ne': 20}}
$in	在范围内	{'age': {'$in': [20, 23]}}
$nin	不在范围内	{'age': {'$nin': [20, 23]}}

$regex	匹配正则表达式	{'name': {'$regex': '^M.*'}}	name以M开头
$exists	属性是否存在	{'name': {'$exists': True}}	name属性存在
$type	类型判断	{'age': {'$type': 'int'}}	age的类型为int
$mod	数字模操作	{'age': {'$mod': [5, 0]}}	年龄模5余0
$text	文本查询	{'$text': {'$search': 'Mike'}}	text类型的属性中包含Mike字符串
$where	高级条件查询	{'$where': 'obj.fans_count == obj.follows_count'}	自身粉丝数等于关注数
## 5.mongodb权限管理
docker exec -it mongo mongo admin
# 创建一个名为 admin，密码为 123456 的用户。
>  db.createUser({ user:'admin',pwd:'123456',roles:[ { role:'userAdminAnyDatabase', db: 'admin'}]});
# 尝试使用上面创建的用户信息进行连接。
> db.auth('admin', '123456')
show dbs          查看数据库

use dbname      进入数据库

show users        查看当前数据库用户权限

MongoDB 创建用户 修改权限 修改密码 删除用户
2
创建用户

db.createUser({user:"usertest",pwd:"passtest",roles:[  {role:"clusterAdmin", db:"admin" }, {role:"readAnyDatabase",db:"admin" }, {role:"readWrite",db:"testDB" } ]})

MongoDB 创建用户 修改权限 修改密码 删除用户
3
权限详解

内建角色：

数据库用户角色：read、readWrite；

数据库管理角色：dbAdmin、dbOwner、userAdmin；

集群管理角色：   clusterAdmin、clusterManager、clusterMonitor、hostManager；

备份恢复角色：   backup、restore；

所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase

超级用户角色：   root；  这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase）

内部角色：          __system；

------------------------------------------------------------------------------------------

角色说明：

Read：                             允许用户读取指定数据库

readWrite：                     允许用户读写指定数据库

dbAdmin：                      允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile

userAdmin：                    允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户

dbOwner：                       允许在当前DB中执行任意操作

readAnyDatabase：          赋予用户所有数据库的读权限，只在admin数据库中可用

readWriteAnyDatabase： 赋予用户所有数据库的读写权限，只在admin数据库中可用

userAdminAnyDatabase：赋予用户所有数据库管理User的权限，只在admin数据库中可用

dbAdminAnyDatabase：   赋予管理所有数据库的权限，只在admin数据库中可用

root：                                 超级账号，超级权限，只在admin数据库中可用。

------------------------------------------------------------------------------------------

集群管理角色：

clusterAdmin：                  赋予管理集群的最高权限，只在admin数据库中可用

clusterManager：               赋予管理和监控集群的权限

clusterMonitor：                赋予监控集群的权限，对监控工具具有readonly的权限

hostManager：                   赋予管理Server

4
修改密码

方法1：db.changeUserPassword("usertest","changepass");

方法2：db.updateUser("usertest",{pwd:"changepass1"})；

MongoDB 创建用户 修改权限 修改密码 删除用户
5
修改权限


db.updateUser("usertest",{roles:[ {role:"read",db:"testDB"} ]})

注：updateuser它是完全替换之前的值，如果要新增或添加roles而不是代替它 

则使用方法： db.grantRolesToUser() 和 db.revokeRolesFromUser(）

------------------------------------------------------------------------------------------

db.grantRolesToUser("usertest", [{role:"readWrite", db:"testDB"},{role:"read", db:"testDB"}])   # 修改权限

db.revokeRolesFromUser("usertest",[{role:"read", db:"testDB"}])   # 删除权限：

MongoDB 创建用户 修改权限 修改密码 删除用户
MongoDB 创建用户 修改权限 修改密码 删除用户
MongoDB 创建用户 修改权限 修改密码 删除用户
6
删除用户

db.dropUser('usertest')

MongoDB 创建用户 修改权限 修改密码 删除用户