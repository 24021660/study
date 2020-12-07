Python使用grequests并发发送请求
目录
前言
grequests简单使用
grequests和requests性能对比
异常处理
前言#
requests是Python发送接口请求非常好用的一个三方库，由K神编写，简单，方便上手快。但是requests发送请求是串行的，即阻塞的。发送完一条请求才能发送另一条请求。
为了提升测试效率，一般我们需要并行发送请求。这里可以使用多线程，或者协程，gevent或者aiohttp，然而使用起来，都相对麻烦。

grequests是K神基于gevent+requests编写的一个并发发送请求的库,使用起来非常简单。

安装方法： pip install gevent grequests
项目地址：https://github.com/spyoungtech/grequests

grequests简单使用#
首先构造一个请求列表，使用grequests.map()并行发送，得到一个响应列表。示例如下。

Copy
import grequests

req_list = [   # 请求列表
    grequests.get('http://httpbin.org/get?a=1&b=2'),
    grequests.post('http://httpbin.org/post', data={'a':1,'b':2}),
    grequests.put('http://httpbin.org/post', json={'a': 1, 'b': 2}),
]

res_list = grequests.map(req_list)    # 并行发送，等最后一个运行完后返回
print(res_list[0].text)  # 打印第一个请求的响应文本

grequests支持get、post、put、delete等requests支持的HTTP请求方法，使用参数和requests一致，发送请求非常简单。
通过遍历res_list可以得到所有请求的返回结果。

grequests和requests性能对比#
我们可以对比下requests串行和grequests并行请求100次github.com的时间，示例如下。
使用requests发送请求

Copy
import requests
import time

start = time.time()
res_list = [requests.get('https://github.com') for i in range(100)]
print(time.time()-start)
实际耗时约100s+

使用grequests发送

Copy
import grequests
import time

start = time.time()
req_list = [grequests.get('https://github.com') for i in range(100)]
res_list = grequests.map(req_list)
print(time.time()-start)
实际耗时约3.58s

异常处理#
在批量发送请求时难免遇到某个请求url无法访问或超时等异常，grequests.map()方法还支持自定义异常处理函数，示例如下。

Copy
import grequests

def err_handler(request, exception):
    print("请求出错")

req_list = [
    grequests.get('http://httpbin.org/delay/1', timeout=0.001),   # 超时异常
    grequests.get('http://fakedomain/'),   # 该域名不存在
    grequests.get('http://httpbin.org/status/500')    #  正常返回500的请求
]

res_list = grequests.map(reqs, exception_handler=err_handler)
print(res_list)
运行结果：

Copy
请求出错
请求出错
[None, None, <Response [500]>]