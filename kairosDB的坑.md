# 数据库安装
从官网地址下载压缩包：`wget https://github.com/kairosdb/kairosdb/releases/download/v1.2.2/kairosdb-1.2.2-1.tar.gz`  
然后解压：`tar -xvf kairosdb-1.2.2-1.tar.gz`  
之后进入解压目录，寻找conf文件夹里面的文件并修改相关参数： `vim kairosdb.properties`  
```conf
主要修改kairosdb的地址端口，以及cassandra的地址与端口
相关的参数：
kairosdb.datastore.cassandra.cql_host_list=39.106.189.206
kairosdb.datastore.cassandra.keyspace=AIOps
```
然后进入bin文件夹运行就可以了：` ./kairosdb.sh run`
后台运行： `./kairosdb.sh start`
# 数据库连接

## 查询数据：
```python
import requests
url = 'https://ts.happit.cn/api/v1/datapoints/query'

def select_data_group_by(tags,name='tag'):
    group_by=[{"name":name,"tags":tags}]
    return group_by

def select_data_aggregators(name,sampling_value='1',unit='minutes',align=None):
    aggregators=[{"name":name,"sampling":{"value":sampling_value,"unit":unit}}]
    return aggregators

def select_data(start_time,end_time,metrics_name,filter_tags=False,group_by=False,aggregators=False):
    #values = {"start_absolute":start_time,"end_relative":end_time,"metrics":[{"tags":{"province":["100"]},"name":"happit.cn.latency","group_by":[{"name": "tag", "tags": ["province"]}],"aggregators": [{"name": "sum", "sampling": {"value": "1", "unit": "minutes"}}]}]}
    values={"start_absolute":start_time,"end_absolute":end_time,"metrics":[{"name":metrics_name}]} ##查询开始时间，结束时间，以及表详细的筛选如计算，group，以及条件见下面判断
    if filter_tags!=False:
        #filter_tags={"province":["100","200"]}
        values['metrics'][0]['tags']=filter_tags
    if group_by!=False:
        #group_by=[{"name":"tag","tags":["province"]}]
        values['metrics'][0]['group_by']=group_by
    if aggregators!=False:
        #aggregators=[{"name":"sum","sampling":{"value":"1","unit":"minutes"}}]
        values['metrics'][0]['aggregators']=aggregators
    r = requests.post(url, data=str(values).encode())
    m=r.json()
    return m
```
## 插入数据：
请求方式`post`
地址：`url=http://localhost:8030/api/v1/datapoints`

代码如下：
```python
data={"name": "archive_file_tracked","datapoints": [[int(time.time())*1000, 123], [1585808126000, 13.2], [1585808111000, 23.1]],"tags": {"host": "server1","data_center": "DC1"},"ttl": 300}
#name 为表名，datapoints为数据点，主要是以时间跟value组成，tags为分维度条件，{}中为维度下的分类条件
m=requests.post(insert_url,data=str(data).encode())
```