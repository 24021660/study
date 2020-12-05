# 1.服务器端安装：


# 2.客户端安装与使用：
## 1.安装客户端：
```
yum install gcc python-devel
yum install krb5-workstation krb5-libs
yum install -y krb5-devel.x86_64
```
## 2.将服务器端文件同步过来
```
/etc/krb5.conf
/var/kerberos/krb5kdc//kadm5.keytab
```
## 3.获取票据：
```
kinit admin/admin
```

# 3.kafka-python使用：
## 1.首先，需要写kafka的jaas文件：
```
KafkaServer {
com.sun.security.auth.module.Krb5LoginModule required
useKeyTab=true
storeKey=true
keyTab="/var/kerberos/krb5kdc/kadm5.keytab"
principal="admin/admin@EXAMPLE.COM";
};
 
KafkaClient {
com.sun.security.auth.module.Krb5LoginModule required
useKeyTab=true
useTicketCache=true
keyTab="/var/kerberos/krb5kdc/kadm5.keytab"
principal="admin/admin@EXAMPLE.COM";
};

Server {
  com.sun.security.auth.module.Krb5LoginModule required
  useKeyTab=true
  keyTab="/var/kerberos/krb5kdc/kadm5.keytab"
  storeKey=true
  useTicketCache=false
  principal="admin/admin@EXAMPLE.COM";
};
Client {
com.sun.security.auth.module.Krb5LoginModule required
useKeyTab=true
useTicketCache=true
keyTab="/var/kerberos/krb5kdc/kadm5.keytab"
principal="admin/admin@EXAMPLE.COM";
};

```
## 2.然后设置环境变量：
```
export KAFKA_OPTS="-Djava.security.auth.login.config=/etc/kafka-jass.conf -Djava.security.krb5.conf=/etc/krb5.conf"
#其中jaas中又有keytab的地址，因此可以关联起来了。
```
## 3.安装python环境
```















1、jaas.conf文件配置：
```conf
KafkaServer {
com.sun.security.auth.module.Krb5LoginModule required
useKeyTab=true
storeKey=true
keyTab="/home/xiehh/kafka_2.10-0.10.0.0/config/kafka.keytab"
principal="xiehh/dap90@ZDH.COM";
};
KafkaClient {
com.sun.security.auth.module.Krb5LoginModule required
useKeyTab=false
useTicketCache=true
renewTicket=true;
};
Client {
com.sun.security.auth.module.Krb5LoginModule required
useKeyTab=true
keyTab="/home/xiehh/kafka_2.10-0.10.0.0/config/kafka.keytab"
storeKey=true
useTicketCache=false
principal="xiehh/dap90@ZDH.COM";
};
```
注：1、其中Client是用来访问zookeeper的，如果未指定serviceName，则默认是zookeeper/dap90@ZDH.COM
2、KafkaClient中的K字母必须大写，小写不识别会报错找不到KafkaClient
3、KafkaClient中我们配置useTicketCache=true，useKeyTab=false ，在执行生产者的时候需要进行kinit操作才能通过kerberos用户认证
也可以通过配置useKeyTab=true方式
```conf
KafkaClient {
com.sun.security.auth.module.Krb5LoginModule required
useKeyTab=true
keyTab="/home/xiehh/kafka_2.10-0.10.0.0/config/kafka.keytab"
storeKey=true
useTicketCache=false
principal="xiehh/dap90@ZDH.COM";
};
```
2、server.properties添加如下配置：
```conf
advertised.host.name=dap90

advertised.listeners=SASL_PLAINTEXT://dap90:9092
listeners=SASL_PLAINTEXT://dap90:9092
security.inter.broker.protocol=SASL_PLAINTEXT
sasl.mechanism.inter.broker.protocol=GSSAPI
sasl.enabled.mechanisms=GSSAPI
sasl.kerberos.service.name=xiehh
```
3、在bin/kafka-run-class.sh脚本中添加kafka jvm参数：
```
JVM performance options

if [ -z "$KAFKA_JVM_PERFORMANCE_OPTS" ]; then
KAFKA_JVM_PERFORMANCE_OPTS="-server -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:+DisableExplicitGC -Djava.awt.headless=true -Djava.security.krb5.conf=/etc/krb5.conf -Djava.security.auth.login.config=/home/xiehh/kafka_2.10-0.10.0.0/config/kafka-jaas.conf -Dzookeeper.sasl.client.username=xiehh"
fi
```
指定Dzookeeper.sasl.client.username为xiehh是因为zk启动用户不是zookeeper时，如果不设置 则获取的principal默认为：zookeeper/dap90@ZDH.COM

4、启动kafka服务器：
```
nohup bin/kafka-server-start.sh config/server.properties &
```
5、配置config/producer.properties，kafka生产者kerberos配置
```
bootstrap.servers=dap90:9092
security.protocol=SASL_PLAINTEXT
sasl.mechanism=GSSAPI
sasl.kerberos.service.name=xiehh
```
启动生产者命令：`bin/kafka-console-producer.sh --broker-list dap90:9092 --topic test --producer.config config/producer.properties`
注：由于KafkaClient {
com.sun.security.auth.module.Krb5LoginModule required
useKeyTab=false
useTicketCache=true
renewTicket=true;
};没有配置keytab 所以在执行bin/kafka-console-producer.sh命令的时候会报错，如下kerberos用户认证失败：
```
[xiehh@dap90 kafka_2.10-0.10.0.0]bin/kafka-console-producer.sh --broker-list dap90:9092 --topic test --producer.config config/producer.properties org.apache.kafka.common.KafkaException: Failed to construct kafka producer at org.apache.kafka.clients.producer.KafkaProducer.(KafkaProducer.java:335) at org.apache.kafka.clients.producer.KafkaProducer.(KafkaProducer.java:188) at kafka.producer.NewShinyProducer.(BaseProducer.scala:40) at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala:45)
at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
Caused by: org.apache.kafka.common.KafkaException: javax.security.auth.login.LoginException: Could not login: the client is being asked for a password, but the Kafka client code does not currently support obtaining a password from the user. not available to garner authentication information from the user
at org.apache.kafka.common.network.SaslChannelBuilder.configure(SaslChannelBuilder.java:86)
at org.apache.kafka.common.network.ChannelBuilders.create(ChannelBuilders.java:70)
at org.apache.kafka.clients.ClientUtils.createChannelBuilder(ClientUtils.java:83)
at org.apache.kafka.clients.producer.KafkaProducer.<init>(KafkaProducer.java:277)
... 4 more
Caused by: javax.security.auth.login.LoginException: Could not login: the client is being asked for a password, but the Kafka client code does not currently support obtaining a password from the user. not available to garner authentication information from the user
at com.sun.security.auth.module.Krb5LoginModule.promptForPass(Krb5LoginModule.java:940)
at com.sun.security.auth.module.Krb5LoginModule.attemptAuthentication(Krb5LoginModule.java:760)
at com.sun.security.auth.module.Krb5LoginModule.login(Krb5LoginModule.java:617)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
at java.lang.reflect.Method.invoke(Method.java:498)
at javax.security.auth.login.LoginContext.invoke(LoginContext.java:755)
at javax.security.auth.login.LoginContext.access000(LoginContext.java:195) at javax.security.auth.login.LoginContext4.run(LoginContext.java:682)
at javax.security.auth.login.LoginContext4.run(LoginContext.java:680) at java.security.AccessController.doPrivileged(Native Method) at javax.security.auth.login.LoginContext.invokePriv(LoginContext.java:680) at javax.security.auth.login.LoginContext.login(LoginContext.java:587) at org.apache.kafka.common.security.authenticator.AbstractLogin.login(AbstractLogin.java:69) at org.apache.kafka.common.security.kerberos.KerberosLogin.login(KerberosLogin.java:110) at org.apache.kafka.common.security.authenticator.LoginManager.(LoginManager.java:46) at org.apache.kafka.common.security.authenticator.LoginManager.acquireLoginManager(LoginManager.java:68) at org.apache.kafka.common.network.SaslChannelBuilder.configure(SaslChannelBuilder.java:78) ... 7 more 解决办法：进行kinit认证：[xiehh@dap90 kafka_2.10-0.10.0.0] kinit -kt config/kafka.keytab xiehh/dap90@ZDH.COM
```
6、配置config/consumer.properties
```
security.protocol=SASL_PLAINTEXT
sasl.mechanism=GSSAPI
sasl.kerberos.service.name=xiehh
```
启动消费者命令：bin/kafka-console-consumer.sh --bootstrap-server dap90:9092 --topic test --new-consumer --from-beginning --consumer.config config/consumer.properties
注：--new-consumer 命令在kafka1.0.0版本已经废弃

7、集成ranger-kafka插件
ranger端也需要开启kerberos，kafka插件也需要在/home/xiehh/kafka_2.10-0.10.0.0/config目录下增加kafka-plugin插件的kerberos开关core-site.xml文件

8、插件集成成功后，功能验证：
切换root用户，执行消费者命令，因为没有kerberos认证权限，会报错：
[root@dap90 kafka_2.10-0.10.0.0]# bin/kafka-console-consumer.sh --bootstrap-server dap90:9092 --topic test --new-consumer --from-beginning --consumer.config config/consumer.properties
[2018-02-08 14:30:31,211] ERROR Unknown error when running consumer: (kafka.tools.ConsoleConsumer) org.apache.kafka.common.KafkaException: Failed to construct kafka consumer at org.apache.kafka.clients.consumer.KafkaConsumer.(KafkaConsumer.java:702) at org.apache.kafka.clients.consumer.KafkaConsumer.(KafkaConsumer.java:587) at org.apache.kafka.clients.consumer.KafkaConsumer.(KafkaConsumer.java:569) at kafka.consumer.NewShinyConsumer.(BaseConsumer.scala:53) at kafka.tools.ConsoleConsumer.run(ConsoleConsumer.scala:64)
at kafka.tools.ConsoleConsumer.main(ConsoleConsumer.scala:51) at kafka.tools.ConsoleConsumer.main(ConsoleConsumer.scala) Caused by: org.apache.kafka.common.KafkaException: javax.security.auth.login.LoginException: Could not login: the client is being asked for a password, but the Kafka client code does not currently support obtaining a password from the user. not available to garner authentication information from the user at org.apache.kafka.common.network.SaslChannelBuilder.configure(SaslChannelBuilder.java:86) at org.apache.kafka.common.network.ChannelBuilders.create(ChannelBuilders.java:70) at org.apache.kafka.clients.ClientUtils.createChannelBuilder(ClientUtils.java:83) at org.apache.kafka.clients.consumer.KafkaConsumer.(KafkaConsumer.java:623) ... 6 more Caused by: javax.security.auth.login.LoginException: Could not login: the client is being asked for a password, but the Kafka client code does not currently support obtaining a password from the user. not available to garner authentication information from the user at com.sun.security.auth.module.Krb5LoginModule.promptForPass(Krb5LoginModule.java:940) at com.sun.security.auth.module.Krb5LoginModule.attemptAuthentication(Krb5LoginModule.java:760) at com.sun.security.auth.module.Krb5LoginModule.login(Krb5LoginModule.java:617) at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) at java.lang.reflect.Method.invoke(Method.java:498) at javax.security.auth.login.LoginContext.invoke(LoginContext.java:755) at javax.security.auth.login.LoginContext.access000(LoginContext.java:195)
at javax.security.auth.login.LoginContext4.run(LoginContext.java:682) at javax.security.auth.login.LoginContext4.run(LoginContext.java:680)
at java.security.AccessController.doPrivileged(Native Method)
at javax.security.auth.login.LoginContext.invokePriv(LoginContext.java:680)
at javax.security.auth.login.LoginContext.login(LoginContext.java:587)
at org.apache.kafka.common.security.authenticator.AbstractLogin.login(AbstractLogin.java:69)
at org.apache.kafka.common.security.kerberos.KerberosLogin.login(KerberosLogin.java:110)
at org.apache.kafka.common.security.authenticator.LoginManager.<init>(LoginManager.java:46)
at org.apache.kafka.common.security.authenticator.LoginManager.acquireLoginManager(LoginManager.java:68)
at org.apache.kafka.common.network.SaslChannelBuilder.configure(SaslChannelBuilder.java:78)
... 9 more
解决办法，需要增加root用户的kerberos认证：
addprinc -randkey root/dap90@ZDH.COM
xst -k root.keytab root/dap90@ZDH.COM
进行kinit操作：[root@dap90 kafka_2.10-0.10.0.0]# kinit -kt /root/keytabs/root.keytab root/dap90@ZDH.COM
再次执行命令，kerberos认证通过，但是又报错root用户没有对topic：test的操作权限如下:
[root@dap90 kafka_2.10-0.10.0.0]# bin/kafka-console-consumer.sh --bootstrap-server dap90:9092 --topic test --new-consumer --from-beginning --consumer.config config/consumer.properties
[2018-02-08 14:33:09,555] WARN The configuration zookeeper.connect = dap90:2181 was supplied but isn't a known config. (org.apache.kafka.clients.consumer.ConsumerConfig)
[2018-02-08 14:33:09,556] WARN The configuration zookeeper.connection.timeout.ms = 6000 was supplied but isn't a known config. (org.apache.kafka.clients.consumer.ConsumerConfig)
[2018-02-08 14:33:09,976] WARN Error while fetching metadata with correlation id 1 : {test=TOPIC_AUTHORIZATION_FAILED} (org.apache.kafka.clients.NetworkClient)
[2018-02-08 14:33:09,978] ERROR Unknown error when running consumer: (kafka.tools.ConsoleConsumer$)
org.apache.kafka.common.errors.TopicAuthorizationException: Not authorized to access topics: [test]

在ranger界面增加policy配置，使root用户可以对test的topic进行操作，暂时只需要赋予consume权限：



捕获.PNG
再执行的时候就ok了，可以消费消息：
[root@dap90 kafka_2.10-0.10.0.0]# bin/kafka-console-consumer.sh --bootstrap-server dap90:9092 --topic test --new-consumer --from-beginning --consumer.config config/consumer.properties
[2018-02-08 14:35:50,823] WARN The configuration zookeeper.connect = dap90:2181 was supplied but isn't a known config. (org.apache.kafka.clients.consumer.ConsumerConfig)
[2018-02-08 14:35:50,824] WARN The configuration zookeeper.connection.timeout.ms = 6000 was supplied but isn't a known config. (org.apache.kafka.clients.consumer.ConsumerConfig)
send a msg
aaaaaa

代码debug，执行bin/kafka-console-consumer.sh命令时，携带kerberos认证用户root：

## 消费：
安装kerberos
然后kafka配置
变量：
export KAFKA_OPTS="-Djava.security.krb5.conf=/etc/krb5.conf -Djava.security.auth.login.config=/app/aiops/"

```
/app/aiops/kafka/kafka_2.11-2.3.0/bin/kafka-console-consumer.sh --whitelist 'jyupay_hb_5|jyupay_hb_info'  --consumer.config=../kafka/kafka_2.11-2.3.0/config/consumer.properties --bootstrap-server=10.252.217.129:6667
```