## 1.安装
   1.java 1.8： `sudo apt install openjdk-8-jdk`   
   2.ssh解密，文件结构要求完全一致
## 2.下载flink(pyflink不需要)
pip install apache-flink
python运行需要更改flink的conf文件：
在conf文件夹下的flink-conf.yaml
添加python3的地址：
`python.client.executable: /usr/bin/python3`
## 3.初步运行demo
```python
from pyflink.dataset import ExecutionEnvironment
from pyflink.table import TableConfig, DataTypes, BatchTableEnvironment
from pyflink.table.descriptors import Schema, OldCsv, FileSystem

exec_env = ExecutionEnvironment.get_execution_environment()
exec_env.set_parallelism(1)
t_config = TableConfig()
t_env = BatchTableEnvironment.create(exec_env, t_config)

t_env.connect(FileSystem().path('input')) \
    .with_format(OldCsv()
                 .field('word', DataTypes.STRING())) \
    .with_schema(Schema()
                 .field('word', DataTypes.STRING())) \
    .create_temporary_table('mySource')

t_env.connect(FileSystem().path('/Volumes/workspace/PycharmProjects/test/flink/output')) \
    .with_format(OldCsv()
                 .field_delimiter('\t')
                 .field('word', DataTypes.STRING())
                 .field('count', DataTypes.BIGINT())) \
    .with_schema(Schema()
                 .field('word', DataTypes.STRING())
                 .field('count', DataTypes.BIGINT())) \
    .create_temporary_table('mySink')

t_env.from_path('mySource') \
    .group_by('word') \
    .select('word, count(1)') \
    .insert_into('mySink')

t_env.execute("tutorial_job")
```
## 4.接入kafka
`bin/kafka-topics.sh --zookeeper 192.168.113.11:2181/kafka --create --replication-factor 1 --partitions 1 --topic flink_test2`
启动生产者-发出测试数据
`bin/kafka-console-producer.sh --broker-list 192.168.113.11:9092 --topic flink_test2`
需要几个重要的jar包：
下载地址：
```
wget -P /opt/flink/lib/ https://repo.maven.apache.org/maven2/org/apache/flink/flink-json/${FLINK_VERSION}/flink-json-${FLINK_VERSION}.jar
wget -P /opt/flink/lib/ https://repo.maven.apache.org/maven2/org/apache/flink/flink-csv/${FLINK_VERSION}/flink-csv-${FLINK_VERSION}.jar
wget -P /opt/flink/lib/ https://repo.maven.apache.org/maven2/org/apache/flink/flink-avro/${FLINK_VERSION}/flink-avro-${FLINK_VERSION}.jar
wget -P /opt/flink/lib/ https://repo.maven.apache.org/maven2/org/apache/flink/flink-jdbc_2.11/${FLINK_VERSION}/flink-jdbc_2.11-${FLINK_VERSION}.jar
wget -P /opt/flink/lib/ https://repo.maven.apache.org/maven2/mysql/mysql-connector-java/8.0.19/mysql-connector-java-8.0.19.jar
wget -P /opt/flink/lib/ https://repo.maven.apache.org/maven2/org/apache/flink/flink-connector-hive_2.11/${FLINK_VERSION}/flink-connector-hive_2.11-${FLINK_VERSION}.jar
wget -P /opt/flink/lib/ https://repo.maven.apache.org/maven2/org/apache/flink/flink-hbase_2.11/${FLINK_VERSION}/flink-hbase_2.11-${FLINK_VERSION}.jar
wget -P /opt/flink/lib/ https://repo.maven.apache.org/maven2/org/apache/flink/flink-connector-hive_2.11/${FLINK_VERSION}/flink-connector-hive_2.11-${FLINK_VERSION}.jar
wget -P /opt/flink/lib/ https://repo1.maven.org/maven2/org/apache/hive/hive-exec/2.3.4/hive-exec-2.3.4.jar
wget -P /opt/flink/lib/ https://repo.maven.apache.org/maven2/org/apache/flink/flink-sql-connector-kafka_2.11/${FLINK_VERSION}/flink-sql-connector-kafka_2.11-${FLINK_VERSION}.jar
wget -P /opt/flink/lib/ https://repo.maven.apache.org/maven2/org/apache/flink/flink-sql-connector-elasticsearch6_2.11/${FLINK_VERSION}/flink-sql-connector-elasticsearch6_2.11-${FLINK_VERSION}.jar
```
其中要对应flink的版本与kafka的版本等
然后运行如下代码：
```python
# -*- coding: UTF-8 -*-
from pyflink.datastream import StreamExecutionEnvironment, CheckpointingMode
from pyflink.table import StreamTableEnvironment, TableConfig, DataTypes, CsvTableSink, WriteMode, SqlDialect



s_env = StreamExecutionEnvironment.get_execution_environment()
s_env.set_parallelism(4)
# 必须开启checkpoint，时间间隔为毫秒，否则不能输出数据
s_env.enable_checkpointing(60000)

st_env = StreamTableEnvironment.create(s_env, TableConfig())
st_env.use_catalog("default_catalog")
st_env.use_database("default_database")
sourceKafkaDdl = """
    CREATE TABLE kafkaTable (
 start_time BIGINT,
 behavior STRING
) WITH (
 'connector' = 'kafka',
 'topic' = 'jyupay_hb_5',
 'properties.bootstrap.servers' = '10.1.95.136:9092',
 'format' = 'json',
 'scan.startup.mode' = 'earliest-offset'
)
    """

a=st_env.execute_sql(sourceKafkaDdl).print()
fieldNames = ["start_time", "behavior"]
fieldTypes = [DataTypes.BIGINT(), DataTypes.STRING()]
csvSink = CsvTableSink(fieldNames, fieldTypes, "/Volumes/workspace/PycharmProjects/test/kafkaoutput.csv", ",", 1, WriteMode.OVERWRITE)
st_env.register_table_sink("csvTableSink", csvSink)

resultQuery = st_env.sql_query("select * from kafkaTable")
resultQuery.insert_into("csvTableSink")

st_env.execute("pyflink-kafka-v2")
```
然后将该代码上传到flink执行：
在flink的bin文件下：
`./flink run -m localhost:8081 -py /home/test/test.py`即可添加任务。

## 5.udf的使用：
目前我们需要在python使用一些函数来对数据做一些处理，一般的函数是没办法使用的，所以我们需要使用udf来对table进行操作。
```python
from pyflink.table import udf

#在pycharm中使用，因为使用了自定义的python函数，所以需要将内存增加到80m，实际上在集群中需要修改config文件
st_env.get_config().get_configuration().set_string("taskmanager.memory.task.off-heap.size","100m")

#如果放到线上的机器，需要安装requirements.txt
st_env.get_config().set_python_executable("python3")
st_env.set_python_requirements("/home/ssd/requirements.txt")


@udf(input_types=[DataTypes.STRING()],
           result_type=DataTypes.ARRAY(DataTypes.STRING()))
  def split(line):
       return line.split(",")
....
#注册该函数：
st_env.register_function("py_split", py_split)
#后面的table就可以使用了
sink_sql_str="select py_split(start_time,0) as message_time,py_split(start_time,1) as message_instance from kafkaTable"
st_env.sql_query(sink_sql_str)
st_env.insert_into('test_table')
st_env.execute('文件名')
```

## 6.新建source和sink
有的时候有些数据库以及接口，在官方是不支持的，所以需要新建source和sink。
```python
class ChartConnector(CustomConnectorDescriptor):
    def __init__(self):
        res=requests.get('http://10.1.60.30:8010/api/v1/metricnames')
        print(res)

```