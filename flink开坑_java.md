## 1.安装java
安装java方法比较多，这里不介绍了
## 2.安装flink
官网下载即可，下载下来之后解压，然后进入bin文件运行命令即可：
```shell
./start-cluster.sh
```
## 3.mvn使用
因为flink开发需要用到相关的依赖，所以，需要使用相关的mvn
mvn搭建
下载对应版本的mvn
然后放到/usr/local/文件夹下
```shell
#修改~/.bashrc
sudo vi ~/.bashrc
#然后输入：
export MAVEN_HOME=/usr/local/apache-maven3.6.3
export PATH=$PATH:$MAVEN_HOME/bin
#应用
source ~/.bashrc


#如果是macos
touch ~/.zshrc
sudo vi ~/.zshrc
#写入
source ~/.bashrc
#应用
source ~/.zshrc
```
修改mvn源：
进入mvn文件夹下的conf文件夹：
```xml
<!--找到<mirror>并替换 -->

<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```
## 4.使用idea开发
在设置中安装scala与mvn
在选项中preferce--》build，Execution，。。。--》build tools --》maven中找到相关路径；
然后编写settings.xml并复制到.m2里面
创建maven项目，创建完成后加入基础依赖。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <flink.version>1.11.1</flink.version>
        <scala.binary.version>2.12</scala.binary.version>
        <scala.version>2.12.8</scala.version>
    </properties>

    <groupId>org.example</groupId>
    <artifactId>flink2</artifactId>
    <version>1.0-SNAPSHOT</version>
    <dependencies>
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-scala_2.12</artifactId>
            <version>1.11.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-streaming-scala_2.12</artifactId>
            <version>1.11.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-clients_${scala.binary.version}</artifactId>
            <version>${flink.version}</version>
        </dependency>
    </dependencies>


</project>


<!--java项目需要依赖 -->
<dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-java</artifactId>
    <version>1.11.1</version>
</dependency>
<dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-streaming-java_2.11</artifactId>
    <version>1.11.1</version>
</dependency>
<dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-clients_2.11</artifactId>
    <version>1.11.1</version>
</dependency>
<!--//scala项目需要依赖 -->
<dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-scala_2.12</artifactId>
    <version>1.11.1</version>
</dependency>
<dependency>
    <groupId>org.apache.flink</groupId>
    <artifactId>flink-streaming-scala_2.12</artifactId>
    <version>1.11.1</version>
</dependency>


// package时将mvn依赖一起打包
```
配置好依赖之后，在pom中右键，然后mvn--》reload即可安装所有依赖
新建一个scala之后可以setupscala
将scala和java文件夹设定为source文件夹
在resource文件夹中新建hello.txt
```txt
hello world
hello flink
hello scala
hello python
how are you
fine thank you and you
```

运行一个wordcount.scala

```
package com.example.wc
import org.apache.flink.api.scala.ExecutionEnvironment
import org.apache.flink.api.scala._


object test{
  def main(args:Array[String]): Unit={
    val env:ExecutionEnvironment = ExecutionEnvironment.getExecutionEnvironment

    val inputPath : String = "/Users/tony/IdeaProjects/flink2/src/main/resources/hello.txt"

    val inputDataSet: DataSet[String]=env.readTextFile(inputPath)

    val resultDataSet: DataSet[(String,Int)] = inputDataSet.flatMap(_.split(" ")).map((_,1)).groupBy(0).sum(1)

    resultDataSet.print()
  }
}
```
文件打包：
file--》project structure--》Artfacts
然后设置main文件之后需要另外设定MF的source文件夹
之后菜单选择build-》build artfact--》build即可
然后放到flink上运行即可
## 5.自定义source
有时候我们需要自定义source
```java
//定义source，目前支持
class MySensorSource extend SourceFunction[SensorReading]{
    //定义flag，即开关
    var running= true

    override def cancel() :Unit =running=false

    override def run(ctx: SourceFunction.Sourcecontext[SensorReading] : Unit ={
        val rand =new random()
        var curTemp = 1.to(10).map(i=>("sensor_"+i,rand.nextDouble()*100))
        while (running){
            curTemp=curTemp.map(data=>(data._1,data._2+rand.nextGaussian()))
            //获取当前时间戳
            val curTime=System.currentTimeMillis()
            //调用collect发出数据
            curTemp.foreach(
                data=>ctx.collect(SensorReading(data._1,curTime,data._2))
            )
            //间隔时间
            Thread.sleep(500)

        }
    }
}
```
定义完了之后，我们使用：
```java
//如果出现evidence parameter of type org.apache.flink.api.common.typeinfo.TypeInformation[sensorreading]的情况，是因为你的streaming包倒入错误了，需要将import org.apache.flink.api.scala._改成import org.apache.flink.streaming.api.scala._
import org.apache.flink.streaming.api.scala._

val steam=env.addSource(new MySensorSource)
steam.print()
```
## 6.自定义sink
有时候我们需要自定义sink：
```java
class MySensorSink extend RichSinkFunction[SensorReading]{
    
}
```


