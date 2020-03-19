## 数据库连接
查询数据：
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