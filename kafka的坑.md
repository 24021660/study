# 1 kafka安装
## 1、kafka需要zookeeper管理，所以需要先安装zookeeper。 

下载zookeeper镜像
```
$ docker pull wurstmeister/zookeeper
```
## 2、启动镜像生成容器
```
docker run -d --name zookeeper -p 2181:2181 -v /etc/localtime:/etc/localtime wurstmeister/zookeeper
$ docker run -d --restart=always --log-driver json-file --log-opt max-size=100m --log-opt max-file=2  --name zookeeper -p 2181:2181 -v /etc/localtime:/etc/localtime wurstmeister/zookeeper
```
## 3、下载kafka镜像
```
$ docker pull wurstmeister/kafka
```
## 4、启动kafka镜像生成容器
```
docker run -d --name kafka -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=172.16.0.13:2181/kafka -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://172.16.0.13:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -v /etc/localtime:/etc/localtime wurstmeister/kafka
$ docker run -d --restart=always --log-driver json-file --log-opt max-size=100m --log-opt max-file=2 --name kafka -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=172.16.0.13:2181/kafka -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://172.16.0.13:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -v /etc/localtime:/etc/localtime wurstmeister/kafka
```
参数说明：
-e KAFKA_BROKER_ID=0  在kafka集群中，每个kafka都有一个BROKER_ID来区分自己

-e KAFKA_ZOOKEEPER_CONNECT=172.16.0.13:2181/kafka 配置zookeeper管理kafka的路径172.16.0.13:2181/kafka

-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://172.16.0.13:9092  把kafka的地址端口注册给zookeeper，如果是远程访问要改成外网IP,类如Java程序访问出现无法连接。

-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 配置kafka的监听端口

-v /etc/localtime:/etc/localtime 容器时间同步虚拟机的时间

## 5、验证kafka是否可以使用

### 5.1、进入容器
```
$ docker exec -it kafka bash
```
### 5.2、进入 /opt/kafka_2.12-2.3.0/bin/ 目录下
```
$ cd /opt/kafka_2.12-2.3.0/bin/
```
### 5.3、运行kafka生产者发送消息
```
$ ./kafka-console-producer.sh --broker-list localhost:9092 --topic sun
```
发送消息
```
> {"datas":[{"channel":"","metric":"temperature","producer":"ijinus","sn":"IJA0101-00002245","time":"1543207156000","value":"80"}],"ver":"1.0"}
 ```
### 5.4、运行kafka消费者接收消息
```
$ ./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic sun --from-beginning
```

# 2 Kafka 的基本术语

`消息`：Kafka 中的数据单元被称为消息，也被称为记录，可以把它看作数据库表中某一行的记录。

`批次`：为了提高效率， 消息会分批次写入 Kafka，批次就代指的是一组消息。

`主题`：消息的种类称为 主题（Topic）,可以说一个主题代表了一类消息。相当于是对消息进行分类。主题就像是数据库中的表。

`分区`：主题可以被分为若干个分区（partition），同一个主题中的分区可以不在一个机器上，有可能会部署在多个机器上，由此来实现 kafka 的伸缩性，单一主题中的分区有序，但是无法保证主题中所有的分区有序

`生产者`：向主题发布消息的客户端应用程序称为生产者（Producer），生产者用于持续不断的向某个主题发送消息。

`消费者`：订阅主题消息的客户端程序称为消费者（Consumer），消费者用于处理生产者产生的消息。

`消费者群组`：生产者与消费者的关系就如同餐厅中的厨师和顾客之间的关系一样，一个厨师对应多个顾客，也就是一个生产者对应多个消费者，消费者群组（Consumer Group）指的就是由一个或多个消费者组成的群体。


`偏移量`：偏移量（Consumer Offset）是一种元数据，它是一个不断递增的整数值，用来记录消费者发生重平衡时的位置，以便用来恢复数据。

`broker`: 一个独立的 Kafka 服务器就被称为 broker，broker 接收来自生产者的消息，为消息设置偏移量，并提交消息到磁盘保存。

`broker 集群`：broker 是集群 的组成部分，broker 集群由一个或多个 broker 组成，每个集群都有一个 broker 同时充当了集群控制器的角色（自动从集群的活跃成员中选举出来）。

`副本`：Kafka 中消息的备份又叫做 副本（Replica），副本的数量是可以配置的，Kafka 定义了两类副本：领导者副本（Leader Replica） 和 追随者副本（Follower Replica），前者对外提供服务，后者只是被动跟随。

`重平衡`：Rebalance。消费者组内某个消费者实例挂掉后，其他消费者实例自动重新分配订阅主题分区的过程。Rebalance 是 Kafka 消费者端实现高可用的重要手段。

# ubuntu下使用
## Step 1: 下载代码
下载 1.0.0版本并解压缩。.
> tar -xzf kafka_2.11-1.0.0.tgz  
> cd kafka_2.11-1.0.0
## Step 2: 启动服务器
Kafka 使用 ZooKeeper 如果你还没有ZooKeeper服务器，你需要先启动一个ZooKeeper服务器。 您可以通过与kafka打包在一起的便捷脚本来快速简单地创建一个单节点ZooKeeper实例。
> bin/zookeeper-server-start.sh config/zookeeper.properties  
[2013-04-22 15:01:37,495] INFO Reading configuration from: config/zookeeper.properties   (org.apache.zookeeper.server.quorum.QuorumPeerConfig)  
...  
现在启动Kafka服务器：
> bin/kafka-server-start.sh config/server.properties  
[2013-04-22 15:01:47,028] INFO Verifying properties (kafka.utils.VerifiableProperties)  
[2013-04-22 15:01:47,051] INFO Property socket.send.buffer.bytes is overridden to 1048576   (kafka.utils.VerifiableProperties)  
...
## Step 3: 创建一个 topic
让我们创建一个名为“test”的topic，它有一个分区和一个副本：
> bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test  
现在我们可以运行list（列表）命令来查看这个topic：
> bin/kafka-topics.sh --list --zookeeper localhost:2181  
test  

或者，您也可将代理配置为：在发布的topic不存在时，自动创建topic，而不是手动创建。
## Step 4: 发送一些消息
Kafka自带一个命令行客户端，它从文件或标准输入中获取输入，并将其作为message（消息）发送到Kafka集群。默认情况下，每行将作为单独的message发送。
运行 producer，然后在控制台输入一些消息以发送到服务器。
> bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test  
This is a message  
This is another message  
## Step 5: 启动一个 consumer
Kafka 还有一个命令行consumer（消费者），将消息转储到标准输出。
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning  
This is a message  
This is another message  

如果您将上述命令在不同的终端中运行，那么现在就可以将消息输入到生产者终端中，并将它们在消费终端中显示出来。  
所有的命令行工具都有其他选项；运行不带任何参数的命令将显示更加详细的使用信息。
## Step 6: 设置多代理集群
到目前为止，我们一直在使用单个代理，这并不好玩。对 Kafka来说，单个代理只是一个大小为一的集群，除了启动更多的代理实例外，没有什么变化。 为了深入了解它，让我们把集群扩展到三个节点（仍然在本地机器上）。
首先，为每个代理创建一个配置文件 (在Windows上使用copy 命令来代替)：
> cp config/server.properties config/server-1.properties    
> cp config/server.properties config/server-2.properties    

现在编辑这些新文件并设置如下属性：
> config/server-1.properties:  
    broker.id=1  
    listeners=PLAINTEXT://:9093  
    log.dir=/tmp/kafka-logs-1  
 
>config/server-2.properties:  
    broker.id=2  
    listeners=PLAINTEXT://:9094  
    log.dir=/tmp/kafka-logs-2  

broker.id属性是集群中每个节点的名称，这一名称是唯一且永久的。我们必须重写端口和日志目录，因为我们在同一台机器上运行这些，我们不希望所有的代理尝试在同一个端口注册，或者覆盖彼此的数据。  
我们已经建立Zookeeper和一个单节点了，现在我们只需要启动两个新的节点：
> bin/kafka-server-start.sh config/server-1.properties &  
...  
> bin/kafka-server-start.sh config/server-2.properties &  
...  

现在创建一个副本为3的新topic：
> bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1   --topic my-replicated-topic  

Good，现在我们有一个集群，但是我们怎么才能知道那些代理在做什么呢？运行"describe topics"命令来查看：
> bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic  
Topic:my-replicated-topic   PartitionCount:1    ReplicationFactor:3 Configs:  
    Topic: my-replicated-topic  Partition: 0    Leader: 1   Replicas: 1,2,0 Isr: 1,2,0

以下是对输出信息的解释。第一行给出了所有分区的摘要，下面的每行都给出了一个分区的信息。因为我们只有一个分区，所以只有一行。

“leader”是负责给定分区所有读写操作的节点。每个节点都是随机选择的部分分区的领导者。  
“replicas”是复制分区日志的节点列表，不管这些节点是leader还是仅仅活着。  
“isr”是一组“同步”replicas，是replicas列表的子集，它活着并被指到leader。  
请注意，在示例中，节点1是该主题中唯一分区的领导者。  
我们可以在已创建的原始主题上运行相同的命令来查看它的位置：
> bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic test  
Topic:test  PartitionCount:1    ReplicationFactor:1 Configs:  
    Topic: test Partition: 0    Leader: 0   Replicas: 0 Isr: 0

这没什么大不了，原来的主题没有副本且在服务器0上。我们创建集群时，这是唯一的服务器。  
让我们发表一些信息给我们的新topic：

> bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my-replicated-topic  
...  
my test message 1  
my test message 2  

现在我们来消费这些消息：
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic   my-replicated-topic  
...  
my test message 1  
my test message 2  

让我们来测试一下容错性。 Broker 1 现在是 leader，让我们来杀了它：
> ps aux | grep server-1.properties  
7564 ttys002    0:15.91 /System/Library/Frameworks/JavaVM.framework/Versions/1.8/Home/bin/java...  
> kill -9 7564  

在 Windows 上用:
> wmic process where "caption = 'java.exe' and commandline like '%server-1.properties%'" get  processid  
ProcessId  
6016  
> taskkill /pid 6016 /f  

领导权已经切换到一个从属节点，而且节点1也不在同步副本集中了：

> bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic  
Topic:my-replicated-topic   PartitionCount:1    ReplicationFactor:3 Configs:  
    Topic: my-replicated-topic  Partition: 0    Leader: 2   Replicas: 1,2,0 Isr: 2,0  

不过，即便原先写入消息的leader已经不在，这些消息仍可用于消费：
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic   my-replicated-topic  
...  
my test message 1  
my test message 2  
## Step 7: 使用Kafka Connect来导入/导出数据
从控制台读出数据并将其写回是十分方便操作的，但你可能需要使用其他来源的数据或将数据从Kafka导出到其他系统。针对这些系统， 你可以使用Kafka Connect来导入或导出数据，而不是写自定义的集成代码。

Kafka Connect是Kafka的一个工具，它可以将数据导入和导出到Kafka。它是一种可扩展工具，通过运行connectors（连接器）， 使用自定义逻辑来实现与外部系统的交互。 在本文中，我们将看到如何使用简单的connectors来运行Kafka Connect，这些connectors 将文件中的数据导入到Kafka topic中，并从中导出数据到一个文件。

首先，我们将创建一些种子数据来进行测试：  
> echo -e "foo\nbar" > test.txt  

在Windows系统使用:
> echo foo> test.txt  
> echo bar>> test.txt  

接下来，我们将启动两个standalone（独立）运行的连接器，这意味着它们各自运行在一个单独的本地专用 进程上。 我们提供三个配置文件。首先是Kafka Connect的配置文件，包含常用的配置，如Kafka brokers连接方式和数据的序列化格式。 其余的配置文件均指定一个要创建的连接器。这些文件包括连接器的唯一名称，类的实例，以及其他连接器所需的配置。
> bin/connect-standalone.sh config/connect-standalone.properties config/  connect-file-source.properties config/connect-file-sink.properties  

这些包含在Kafka中的示例配置文件使用您之前启动的默认本地群集配置，并创建两个连接器： 第一个是源连接器，用于从输入文件读取行，并将其输入到 Kafka topic。 第二个是接收器连接器，它从Kafka topic中读取消息，并在输出文件中生成一行。

在启动过程中，你会看到一些日志消息，包括一些连接器正在实例化的指示。 一旦Kafka Connect进程启动，源连接器就开始从 test.txt 读取行并且 将它们生产到主题 connect-test 中，同时接收器连接器也开始从主题 connect-test 中读取消息， 并将它们写入文件 test.sink.txt 中。我们可以通过检查输出文件的内容来验证数据是否已通过整个pipeline进行交付：
> more test.sink.txt  
foo  
bar  

请注意，数据存储在Kafka topic connect-test 中，因此我们也可以运行一个console consumer（控制台消费者）来查看 topic 中的数据（或使用custom consumer（自定义消费者）代码进行处理）：
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic connect-test   --from-beginning  
{"schema":{"type":"string","optional":false},"payload":"foo"}  
{"schema":{"type":"string","optional":false},"payload":"bar"}  
...

连接器一直在处理数据，所以我们可以将数据添加到文件中，并看到它在pipeline 中移动：

> echo Another line>> test.txt

您应该可以看到这一行出现在控制台用户输出和接收器文件中。

## Step 8:使用 Kafka Streams 来处理数据
Kafka Streams是用于构建实时关键应用程序和微服务的客户端库，输入与输出数据存储在Kafka集群中。 Kafka Streams把客户端能够轻便地编写部署标准Java和Scala应用程序的优势与Kafka服务器端集群技术相结合，使这些应用程序具有高度伸缩性、弹性、容错性、分布式等特性。 本快速入门示例将演示如何运行一个基于该库编程的流式应用程序。

## kafka开启kerberos认证
当kafka开启Kerberos认证后，如何使用java API生产或消费数据呢？

其实就是在生产消费者的代码中加入jaas、keytab这些认证有关的配置，下面我们直接看代码：

 

认证配置文件

kafka_client_jaas.conf
```
KafkaClient {
        com.sun.security.auth.module.Krb5LoginModule required
        useKeyTab=true
        keyTab="D:\\resources\\user1.keytab"
        storeKey=true
        useTicketCache=false
        principal="user1@TEST.COM"
        serviceName=kafka;
    };

Client {
        com.sun.security.auth.module.Krb5LoginModule required
        useKeyTab=true
        keyTab="D:\\resources\\user1.keytab"
        storeKey=true
        useTicketCache=false
        principal="user1@TEST.COM"
       serviceName=kafka;
};
```
krb5.conf
```
#File modified by ipa-client-install

includedir /var/lib/sss/pubconf/krb5.include.d/

[libdefaults]
  default_realm = TEST.COM
  dns_lookup_realm = false
  dns_lookup_kdc = false
  rdns = false
  ticket_lifetime = 24h
  renew_lifetime = 36h
  forwardable = yes
  udp_preference_limit = 0


[realms]
  HAOHANDATA.COM = {
    kdc = dn2.test.com:88
    master_kdc = dn2.test.com:88
    admin_server = dn2.test.com:749
    default_domain = test.COM
    pkinit_anchors = FILE:/etc/ipa/ca.crt
  }


[domain_realm]
  .TEST.COM = TEST.COM
  TEST.COM = TEST.COM
  .test.com = TEST.COM
  test.com = TEST.COM
```
 

消费者

需要添加认证有关的环境变量，有两种方式：

直接在代码中使用System.setProperty进行设置
启动JVM时设置，使用-D传递参数
在此示例中，直接在代码中设置：

/**
 * 消费者
 */
 ```
public class ConsumerTest {


    public static void main(String[] args) {

        //在windows中设置JAAS，也可以通过-D方式传入
        System.setProperty("java.security.auth.login.config", "D:\\resources\\kafka_client_jaas.conf");
        System.setProperty("java.security.krb5.conf", "D:\\resources\\krb5.conf");
        //在Linux中设置JAAS，也可以通过-D方式传入
//        System.setProperty("java.security.auth.login.config", "/jaas/kafka_client_jaas.conf");
//        System.setProperty("java.security.krb5.conf", "/etc/krb5.conf");


        Properties props = new Properties();
        // 定义kakfa 服务的地址，不需要将所有broker指定上
        props.put("bootstrap.servers", "10.10.2.19:9092,10.10.2.18:9092");
        // 制定consumer group
        props.put("group.id", "hhtest");
        // 是否自动确认offset
        props.put("enable.auto.commit", "true");

        props.put("auto.offset.reset", "earliest");

        // 自动确认offset的时间间隔
        props.put("auto.commit.interval.ms", "1000");
        // key的序列化类
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        // value的序列化类
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

        props.put("security.protocol", "SASL_PLAINTEXT");
        props.put("sasl.kerberos.service.name", "kafka");
        props.put("sasl.mechanism", "GSSAPI");


        // 定义consumer
        KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(props);

        // 消费者订阅的topic, 可同时订阅多个
        consumer.subscribe(Arrays.asList("translog"));

        while (true) {
            // 读取数据，读取超时时间为100ms
            ConsumerRecords<String, String> records = consumer.poll(100);

            for (ConsumerRecord<String, String> record : records)

                System.out.printf("partition = %s, offset = %d, key = %s, value = %s%n",record.partition(), record.offset(), record.key(), record.value());
        }
    }

}
```
生产者
```
public class ProducerTest {

    public static void main(String[] args) {

        //在windows中设置JAAS，也可以通过-D方式传入
        System.setProperty("java.security.auth.login.config", "D:\\resources\\kafka_client_jaas.conf");
        System.setProperty("java.security.krb5.conf", "D:\\resources\\krb5.conf");
        //在Linux中设置JAAS，也可以通过-D方式传入
        //System.setProperty("java.security.auth.login.config", "/jaas/kafka_client_jaas.conf");
        //System.setProperty("java.security.krb5.conf", "/etc/krb5.conf");


        Properties props = new Properties();
        props.put("bootstrap.servers", "192.168.2.19:9092,192.168.2.18:9092");
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("security.protocol", "SASL_PLAINTEXT");
        props.put("sasl.kerberos.service.name", "kafka");
        props.put("sasl.mechanism", "GSSAPI");
        KafkaProducer<String,String> producer = new KafkaProducer<String, String>(props);

        String topic = "demoTopic";

        Scanner scan  = new Scanner(System.in);

        while (true){
            System.out.print(">>");
            String message = scan.nextLine();
            producer.send(new ProducerRecord<String, String>(topic, message));
            System.out.println(message);
            try {
                Thread.sleep(200);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

    }

}
```
## 后台启动zookepeer跟kafka
--deamon
