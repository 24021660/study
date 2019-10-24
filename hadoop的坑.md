# 1.安装hadoop
首先集群的username必须一致，否则集群登不上去
## （1）检查java是否安装
首先，查看是否装有java:`Java -version`  
如果没有，则安装java：`sudo apt-get install default-jdk`  
查看sdk路径：`update-alternatives --display java`  
## （2）安装ssh
`sudo apt-get install ssh`
## （3）安装rsync
`sudo apt-get install rsync`
## (4)产生ssh key 使得以后不再弹出ssh登录
`ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa`
如果不行就用rsa
`ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa`
查看ssh key：`ll ~/.ssh`
## (5)产生key放置到许可证文件中
`cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys`
rsa的用下面指令
`cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`
## (6)Hadoop安装
登录hadoop官网：
`http://archive.apache.org/dist/hadoop/common`  
找到相应版本，复制链接：
`http://archive.apache.org/dist/hadoop/common/hadoop-2.6.4/hadoop-2.6.4.tar.gz`  
进入终端，然后使用`wget http://archive.apache.org/dist/hadoop/common/hadoop-2.6.4/hadoop-2.6.4.tar.gz`下载  
如果觉得慢，可以使用阿里云的镜像源：
`http://mirrors.aliyun.com/apache/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz`  
解压缩：`sudo tar  -zxvf hadoop-2.7.7.tar.gz`  
移动到目录:`sudo mv hadoop-2.7.7 /usr/local/hadoop`
查看目录：`ll /usr/local/hadoop`
## (7)设置Hadoop环境变量
编辑bashrc：`sudo vim ~/.bashrc`
输入以下内容：
```conf
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"
export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native:$JAVA_LIBRARY_PATH
```
让上述指令生效：`source ~/.bashrc`
## (8)修改Hadoop配置文件
### 设置`hadoop-env.sh`配置文件:
`sudo vim /usr/local/hadoop/etc/hadoop/hadoop-env.sh`
将原本的JAVA_HOME
```conf
export JAVA_HOME=${JAVA_HOME}
```
改为：
```conf
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```
### 设置`core-site.xml`文件
`sudo vim /usr/local/hadoop/etc/hadoop/core-site.xml`
在`configuration`标签中加入
```html
 <property>
    <name>fs.default.name</name>
    <value>hdfs://localhost:9000</value>
</property>
```
### 编辑YARN-site.xml
`sudo vim /usr/local/hadoop/etc/hadoop/yarn-site.xml`
在`configuration`中加入
```html
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
<property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
```
### 设置mapred-site.xml
复制模板文件：由mapred-site.xml.template至mapred-site.xml
`sudo cp /usr/local/hadoop/etc/hadoop/mapred-site.xml.template /usr/local/hadoop/etc/hadoop/mapred-site.xml`
### 编辑mapred-site.xml
`sudo vim /usr/local/hadoop/etc/hadoop/mapred-site.xml`
在`configurarion`中加入
```html
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
```
### 设置hdfs-site.xml
`sudo vim /usr/local/hadoop/etc/hadoop/hdfs-site.xml`
在`configurarion`中输入
```html
<property>
    <name>dfs.replication</name>
    <value>3</value>
</property>
<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/usr/local/hadoop/hadoop_data/hdfs/namenode</value>
</property>
<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/usr/local/hadoop/hadoop_data/hdfs/datanode</value>
</property>
```
## (9)创建并格式化HDFS目录
### 创建namenode数据存储目录
`sudo mkdir -p /usr/local/hadoop/hadoop_data/hdfs/namenode`
### 创建datanode数据存储目录
`sudo mkdir -p /usr/local/hadoop/hadoop_data/hdfs/datanode`
### 将hadoop目录所有者改为hduser
`sudo chown hduser:hduser -R /usr/local/hadoop`
### 格式化namenode
`hadoop namenode -format`
# 2.启动Hadoop
启动hdfs：`start-dfs.sh`  
启动yarn：`start-yarn.sh`
或者同时启动：`start-all.sh`
# 3.打开界面
输入`localhost:8088`

# 4.分布式安装
## （1）机器基本配置
首先，IP地址分别为：`192.168.245.139`,`192.168.245.146`,`192.168.245.147`  
按照之前的步骤将三台机器装上hadoop  
现在想要使用192.168.245.139作为master，其他为data1，2，3  
将几台机器，更改服务器名称：`sudo vim /etc/hostname`，然后更改相应的名称
然后想要对几台机器进行绑定：
每台机器上输入：`sudo vim /etc/hosts`，然后在文件中增加
```c++
192.168.245.139  master
192.168.245.147  tony2
192.168.245.146  tony3
192.168.1.91     tony1
```
## （2）data机器设置
### 设置HDFS名称
用于存取的时候使用：`sudo vim /usr/local/hadoop/etc/hadoop/core-site.xml`
将原来的
```html
<property>
    <name>fs.default.name</name>
    <value>hdfs://localhost:9000</value>
</property>
```
改为
```html
<property>
    <name>fs.default.name</name>
    <value>hdfs://master:9000</value>
</property>
```
### 设置yarn
使用`sudo vim /usr/local/hadoop/etc/hadoop/yarn-site.xml`
在原来的
```html
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
<property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
```
基础上加上三个参数，然后变成
```html
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
<property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
<property>
    <name>yarn.resourcemanager.resource-tracker.address</name>
    <value>master:8025</value>
</property>
<property>
    <name>yarn.resourcemanager.scheduler.address</name>
    <value>master:8030</value>
</property>
<property>
    <name>yarn.resourcemanager.address</name>
    <value>master:8050</value>
</property>
```
### 设置mapred
`sudo vim /usr/local/hadoop/etc/hadoop/mapred-site.xml`
将原来的
```html
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
```
改为
```html
<property>
    <name>mapred.job.tracker</name>
    <value>master:54311</value>
</property>
```
### 设置hdfs
`sudo vim /usr/local/hadoop/etc/hadoop/hdfs-site.xml`
将原来的
```html
<property>
    <name>dfs.replication</name>
    <value>3</value>
</property>
<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/usr/local/hadoop/hadoop_data/hdfs/namenode</value>
</property>
<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/usr/local/hadoop/hadoop_data/hdfs/datanode</value>
</property>
```
去掉namenode，改为
```html
<property>
    <name>dfs.replication</name>
    <value>3</value>
</property>
<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/usr/local/hadoop/hadoop_data/hdfs/datanode</value>
</property>
```
## （3）master机器配置
master机器除了上面的修改之外，需要继续修改
### hdfs设置
`sudo vim /usr/local/hadoop/etc/hadoop/hdfs-site.xml`
将原来的
```html
<property>
    <name>dfs.replication</name>
    <value>3</value>
</property>
<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/usr/local/hadoop/hadoop_data/hdfs/datanode</value>
</property>
```
中的datanode改为namenode
```html
<property>
    <name>dfs.replication</name>
    <value>3</value>
</property>
<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/usr/local/hadoop/hadoop_data/hdfs/namenode</value>
</property>
```
### 编辑master文件
`sudo vim /usr/local/hadoop/etc/hadoop/masters`  
在文件中将名称改为`master`  
### 编辑slaves文件
`sudo vim /usr/local/hadoop/etc/hadoop/slaves`  
将文件中的名称改为`data1`,`data2`,`data3`  
### (4)初始化，创建hdfs目录 
使用master，ssh连入data1，2，3  
删除hdfs目录  
`sudo rm -rf /usr/local/hadoop/hadoop_data/hdfs`  
创建datanode目录  
`sudo mkdir -p /usr/local/hadoop/hadoop_data/hdfs/datanode`  
将目录所有者更改为hduser  
`sudo chown -R hduser:hduser /usr/local/hadoop`  
### (5)初始化namenode目录
删除hdfs目录  
`sudo rm -rf /usr/local/hadoop/hadoop_data/hdfs`  
创建namenode目录  
`sudo mkdir -p /usr/local/hadoop/hadoop_data/hdfs/namenode`  
将目录所有者更改为hduser  
`sudo chown -R hduser:hduser /usr/local/hadoop`  
格式化namenode目录  
`hadoop namenode -format`  
### (5)启动
方式与单机版一样
# 3.hadoop hdfs基本操作
`hadoop fs -mkdir`:创建目录
`hadoop fs -ls`：列出目录
`hadoop fs -copyFromLocal`：本地复制到HDFS
`hadoop fs -put`:复制到HDFS
`hadoop fs -cat`:列出目录下文件内容
`hadoop fs -copyToLocal`：将hdfs文件复制到本地
`hadoop fs -get`：将hdfs文件复制到本地
`hadoop fs -cp`：复制hdfs文件
`hadoop fs -rm`：删除hdfs文件


















