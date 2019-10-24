# 1.spark安装
## scala安装
因为spark是scala开发的，所以必须先安装scala
先下载scala(国内源)：`wget http://distfiles.macports.org/scala2.11/scala-2.11.8.tgz`
解压缩：`tar xvf scala-2.11.8.tgz`
迁移到usr/local:`sudo mv scala-2.11.8 /usr/local/scala`
编辑~/.bashrc:`sudo vim ~/.bashrc`
输入以下指令：
```conf
#SCALA Varibles
export SCALA_HOME=/usr/local/scala
export PATH=$SCALA_HOME/bin
#SCALA Varibles
```
然后执行生效：`source ~/.bashrc`
测试：输入scala，进入之后输入1+1回车，会出现2，输入：q退出
## 安装spark
首先选择版本，因为是与Hadoop配合，所以需要选择版本，之前如果安装的hadoop2.7版本，则spark需要对应2.7版本（阿里源）：
`wget http://mirrors.aliyun.com/apache/spark/spark-2.3.4/spark-2.3.4-bin-hadoop2.7.tgz`
仍然是解压缩：`tar zxf spark2-2.3.4-bin-hadoop2.7.tgz`
移动到usr/local/spark:`sudo mv spark2-2.3.4-bin-hadoop2.7 /usr/local/spark`
环境变量设置：sudo vim ~/.bashrc,输入：
```conf
export SPARK_HOME=/usr/local/spark
export PATH=$SPARK_HOME/bin
```
然后执行生效：`source ~/.bashrc`
# 2.启动spark
`pyspark`
退出：`exit()`
# 3.使用spark
首先，为了测试，将一段文本上传到hadoop的user文件夹中。
## 本地运行pyspark
`pyspark--master local[4]`:其中local[]代表开几个进程，[*]代表尽量使用进程
`sc.master`:查看当前状态
`textFile=sc.textfile("file:/usr/local/spark/README.md")`:读取本地文件
`textFile.count()`:获取行数
`textFile=sc.textfile("hdfs:/master:9000/user/README.md")`:读取hdfs文件
## Hadoop YARN 运行pyspark
`HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop pyspark --master yarn -deploy -mode client`
该模式可以在web端，8090 application中查看
## spark standalone 没有hadoop的情况下做集群运算
master设置spark-env.sh：`cp /usr/local/spark/conf/spark-env.sh.template /usr/local/spark/conf/spark-env.sh`
编辑spark-env：`sudo vim /usr/local/spark/conf/spark-env.sh`
```conf
export SPARK_MASTER_IP=master
export SPARK_WORKER_CORES=1
export SPARK_WORKER_MEMORY=512m
export SPARK_WORKER_INSTANCES=4
```

复制到slave
编辑slave



