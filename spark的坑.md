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
export PATH=$PATH:$SCALA_HOME/bin
#SCALA Varibles
```
然后执行生效：`source ~/.bashrc`
测试：输入scala，进入之后输入1+1回车，会出现2，输入：q退出
## 安装spark
首先选择版本，因为是与Hadoop配合，所以需要选择版本，之前如果安装的hadoop2.7版本，则spark需要对应2.7版本（阿里源）：
`wget http://mirrors.aliyun.com/apache/spark/spark-2.3.4/spark-2.3.4-bin-hadoop2.7.tgz`  
仍然是解压缩：`tar zxf spark-2.3.4-bin-hadoop2.7.tgz`  
移动到usr/local/spark:`sudo mv spark-2.3.4-bin-hadoop2.7 /usr/local/spark`  
环境变量设置：sudo vim ~/.bashrc,输入：
```conf
export SPARK_HOME=/usr/local/spark
export PATH=$PATH:$SPARK_HOME/bin
```
然后执行生效：`source ~/.bashrc`
# 2.启动spark
如果使用的是python3,需要在bashrc中输入python3的信息
`export PYSPARK_PYTHON=python3`
然后执行`pyspark`
退出：`exit()`
# 3.使用spark
首先，为了测试，将一段文本上传到hadoop的user文件夹中。
## 本地运行pyspark
`pyspark--master local[4]`:其中local[]代表开几个进程，[*]代表尽量使用进程  
`sc.master`:查看当前状态  
`textFile=sc.textFile("file:/usr/local/spark/README.md")`:读取本地文件  
`textFile.count()`:获取行数  
`textFile=sc.textFile("hdfs://master:9000/user/README.md")`:读取hdfs文件 (//双斜杠) 
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
`sudo scp -r /usr/local/spark tony@s1:/usr/local/`

编辑slave  
`sudo vim /usr/local/spark/conf/slaves`  
打开之后，编辑：
```
tony
tony2
tony3
```
运行spark standalone  
启动spark standalone：`/usr/spark/sbin/start-all.sh`  
也可以分别启动master与slaves：`/usr/spark/sbin/start-master.sh`,`/usr/spark/sbin/start-slaves.sh`  
在standalone上运行pyspark：  
`pyspark --master spark://master:7077 --num-executors 1 --total-executor-core 3 --executor-memory 512m`  
spark web ui界面
`http://master:8080/`

# 3.使用anaconda操作spark
安装：`sudo apt-get anaconda`
设置anaconda的路径：sudo vim ~/.bashrc
输入：
```conf
export PATH=/home/tony/anaconda3/bin:$PATH
export ANACONDA_PATH=/home/tony/anaconda3
export PYSPARK_DRIVER_PYTHON=$ANACONDA_PATH/bin/ipython
export PYSPARK_PYTHON=$ANACONDA_PATH/bin/python
```
执行生效：source ~/.bashrc
在s1，s2，s3分别安装

# 3.spark RDD
## 创建RDD
### （1）int
```python
intRDD=sc.parallelize(List(3,1,2,5,5)) #这是一个转换
intRDD.collect()   #这是一个动作，立即执行
```
### （2）string
```python
stringRDD=sc.parallelize(List("apple","orange","Banana","Grape","Apple"))
stringRDD.collect()
```
## 基本运算
### (1)map运算
显式：
```python
def addOne():
    return x+1
intRDD.map(addOne).collect()   #使rdd中每个元素+1
```
隐式：
```python
intRDD.map(x=>x+1).collect()
```
### (2)filter运算
```python
intRDD.filter(lambda x:x<3).collect() 
intRDD.filter(lambda x:x==3).collect() 
intRDD.filter(lambda x:x>3 and x<5>).collect()
intRDD.filter(lambda x:x<3 or x>5).collect()
stringRDD.filter(lambda x:'ra' in x).collect()   
```
### (3)distinct运算
删除重复元素
```
intRDD.distinct().collect()  #原来为[3,1,2,5,5],执行之后变成[1,2,3,5]
stringRDD.distinct().collect() #同理
```
### （4）ramdomSplit
整个元素集合以随机数方式，按照比例分开
```python 
sRDD=intRDD.randomSplit([0.4,0.6])
sRDD[0].collect()
sRDD[1].collect()
```
### (5) groupBy
分组：
```python
gRDD=intRDD.groupBy(lambda x:'even' if(x%2==0) else 'odd').collect()
print(gRDD[0][0],sorted(gRDD[0][1])) #('even',[2])
print(gRDD[1][0],sorted(gRDD[1][1])) #('odd',[1,3,5,5])
```
## 多集合运算
### (1)union
```python
intRDD1.union(intRDD2).union(intRDD3).collect() #将三个数组合并，并集运算
```
### （2）intersection
```python
intRDD1.intersection(intRDD2).collect() #取交集
```
### （3）差集运算
```python
intRDD1.subtract(intRDD2).collect() #取差集集，[3,1,2,5,5],[5,6],去掉重复部分
```
### （4）cartesian笛卡尔积
```python
intRDD1.cartesian(intRDD2).collect() #[3,1,2,5,5],[5,6],最后得到[(3,5),(3,6),(1,5),(1,6),(),(),(),(),(),()]
```
## 动作运算
### (1)读取元素
```python
intRDD.first()  #取第一项
intRDD.take(2)  #取前两项
intRDD.takeordered(3)   #取前三项，从小到大
intRDD.takeordered(3,key=lambda x:-x) #取前三项，从大到小
```
### （2）统计
```python
intRDD.stats()  #统计 ，（count：5，mean：3.2,max:5.0,min:1.0）
intRDD.min()  #取最小
intRDD.max()   #最大
intRDD.stdev() #标准差
intRDD.count()  #计数
intRDD.sum()  #求和
intRDD.mean()   #取平均数
```
## key-value 转换运算
```python
kvRDD1=sc.parallelize([(3,4),(3,6),(5,6),(1,2)])
kvRDD1.keys().collect() #取全部key值，[3,3,5,1]
kvRDD1.values().collect() #取全部values值，[4,6,6,2]
kvRDD1.filter(lambda keyValue:keyValue[0]<5).collect() #key<5的key-value
kvRDD1.filter(lambda keyValue:keyValue[1]<5).collect() #value<5的key-value
kvRDD1.mapValues(lambda x:x*x).collect() #只将value值平方
kvRDD1.sortByKey(ascending=True).collect() #按照key从小到大排列，false相反
kvRDD1.reduceByKey(lambda x,y:x+y).collect() #取key的值相同的value相加，不同的不相加
```
## 多个key-value转换运算
```python
kvRDD1=sc.parallelize([(3,4),(3,6),(5,6),(1,2)])
kvRDD2=sc.parallelize([(3,8)])
kvRDD1.join(kvRDD2).collect() #key值相同的进行join[(3,(4,8)),(3,(6,8))]
kvRDD1.leftOuterJoin(kvRDD2).collect() #key值相同的进行join,不相同的也加入[(1,(2,none)),(3,(4,8)),(3,(6,8)),(5,(6,none))]
kvRDD1.rightOuterJoin(kvRDD2).collect() #key值相同的进行右边对比join,不相同的也加入
kvRDD1.subtractByKey(kvRDD2).collect() #key值相同的删掉，剩下的[(1,2),(5,6)]
```
## key-value动作运算
```python
kvRDD1.first() #取第一个key-value
kvfirst=kvRDD1.first()
kvfirst[0]  #取key
kvfirst[1]   #取value

kvRDD1.countByKey()  #按照key的值，计算个数，{1:1,3:2,5:1}

kv=kvRDD1.collectAsMap()   #变成字典使用，重复的键值合并成一个{1:2,3:6,5:6}
kv[3] #输出第三个字典中的值，6

kvRDD1.lookup(3) #通过输入键值，找value，如果有多个值，则合并{4，6}
```

## 广播变量
用于多次使用，调用的场景,节省内存与调用时间
```python
kvFruit=sc.parallelize([(1,"apple"),(2,"orange"),(3,"banana"),(4,"grape")])#创建key-value
fruitMap=kvFruit.collectAsMap() #转换成字典
bcFruitMap=sc.broadcast(fruitMap)  #字典转换成广播变量
fruitIds=sc.parallelize([2,4,1,3])
fruitNames=fruitIds.map(lambda x:bcFruitMap.value[x]).collect()
```
### 累加器
对于数值要进行累加计算的，节省内存与调用时间
```python
intRDD=sc.parallelize([3,1,2,5,5])
total=sc.accumulator(0.0) #total累加器
num=sc.accumulator(0)  #num累加器
intRDD.foreach(lambda i:[total.add(i),num.add(1)])
avg=total.value/num.value
#将执行3+1+2+5+5
```
### RDD持久化
将需要重复调用的数据存到内存中，便于重复调用
```python
intRDD=sc.parallelize([1,2,3,4])
intRDD.persist()   #持久化
intRDD.unpersist()  #取消持久化
```
### 实例
```python
#text.txt中文件为：
#apple apple orange 
#banana grape grape
#然后我们要统计单词出现了多少次
textFile=sc.textFile("data/text.txt") #读取文本文件
stringRDD=textFile.flatmap(lambda x:x.split(' ')) #平滑分割单词
countsRDD=stringRDD.map(lambda x:(x,1)).reduceByKey(lambda x,y:x+y).collect() #将单词map成次数，并进行相加
```








