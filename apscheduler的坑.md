# 安装 APScheduler
`$ pip install apscheduler`
# 快速开始
```python
from apscheduler.schedulers.blocking import BlockingScheduler

scheduler = BlockingScheduler()

@scheduler.scheduled_job('cron', hour='8-23')
def request_update_status():
    print('Doing job')

scheduler.start()
```
# 基本概念
## APScheduler四大组件：

触发器 `triggers` ：用于设定触发任务的条件  
任务储存器 `job stores`：用于存放任务，把任务存放在内存或数据库中  
执行器 `executors`： 用于执行任务，可以设定执行模式为单线程或线程池  
调度器 `schedulers`： 把上方三个组件作为参数，通过创建调度器实例来运行  
### 触发器
每一个任务都有自己的触发器，触发器用于决定任务下次运行的时间。

### 任务储存器
默认情况下，任务存放在内存中。也可以配置存放在不同类型的数据库中。如果任务存放在数据库中，那么任务的存取有一个序列化和反序列化的过程，同时修改和搜索任务的功能也是由任务储存器实现。

注！一个任务储存器不要共享给多个调度器，否则会导致状态混乱

### 执行器
任务会被执行器放入线程池或进程池去执行，执行完毕后，执行器会通知调度器。

### 调度器
一个调度器由上方三个组件构成，一般来说，一个程序只要有一个调度器就可以了。开发者也不必直接操作任务储存器、执行器以及触发器，因为调度器提供了统一的接口，通过调度器就可以操作组件，比如任务的增删改查。

### 调度器组件详解
根据开发需求选择相应的组件，下面是不同的调度器组件：

`BlockingScheduler` 阻塞式调度器：适用于只跑调度器的程序。
`BackgroundScheduler` 后台调度器：适用于非阻塞的情况，调度器会在后台独立运行。
`AsyncIOScheduler` AsyncIO调度器，适用于应用使用AsnycIO的情况。
`GeventScheduler` Gevent调度器，适用于应用通过Gevent的情况。
`TornadoScheduler` Tornado调度器，适用于构建Tornado应用。
`TwistedScheduler` Twisted调度器，适用于构建Twisted应用。
`QtScheduler` Qt调度器，适用于构建Qt应用。
任务储存器的选择，要看任务是否需要持久化。如果你运行的任务是无状态的，选择默认任务储存器`MemoryJobStore`就可以应付。但是，如果你需要在程序关闭或重启时，保存任务的状态，那么就要选择持久化的任务储存器。如果，作者推荐使用`SQLAlchemyJobStore`并搭配PostgreSQL作为后台数据库。这个方案可以提供强大的数据整合与保护功能。

执行器的选择，同样要看你的实际需求。默认的`ThreadPoolExecutor`线程池执行器方案可以满足大部分需求。如果，你的程序是计算密集型的，那么最好用`ProcessPoolExecutor`进程池执行器方案来充分利用多核算力。也可以将`ProcessPoolExecutor`作为第二执行器，混合使用两种不同的执行器。

配置一个任务，就要设置一个任务触发器。触发器可以设定任务运行的周期、次数和时间。APScheduler有三种内置的触发器：

`date` 日期：触发任务运行的具体日期
`interval` 间隔：触发任务运行的时间间隔
`cron` 周期：触发任务运行的周期
一个任务也可以设定多种触发器，比如，可以设定同时满足所有触发器条件而触发，或者满足一项即触发。复合触发器，请查阅一下文档：链接
### 触发器详解
date 在指定时间点触发任务
```python
from datetime import date
from apscheduler.schedulers.blocking import BlockingScheduler

sched = BlockingScheduler()

def my_job(text):
    print(text)

# 在2009年11月6日执行
sched.add_job(my_job, 'date', run_date=date(2009, 11, 6), args=['text'])

sched.start()
```
其中run_date参数可以是date类型、datetime类型或文本类型。

datetime类型（用于精确时间）
```python
# 在2009年11月6日 16:30:05执行
sched.add_job(my_job, 'date', run_date=datetime(2009, 11, 6, 16, 30, 5), args=['text'])
```
文本类型
```python
sched.add_job(my_job, 'date', run_date='2009-11-06 16:30:05', args=['text'])
```
未指定时间，则会立即执行
```python
# 未显式指定，那么则立即执行
sched.add_job(my_job, args=['text'])
```
interval 周期触发任务
```python
from datetime import datetime
from apscheduler.schedulers.blocking import BlockingScheduler

def job_function():
    print("Hello World")

sched = BlockingScheduler()

# 每2小时触发
sched.add_job(job_function, 'interval', hours=2)

sched.start()
```
你可以框定周期开始时间start_date和结束时间end_date。
```python
# 周期触发的时间范围在2010-10-10 9:30 至 2014-06-15 11:00
sched.add_job(job_function, 'interval', hours=2, start_date='2010-10-10 09:30:00', end_date='2014-06-15 11:00:00')
```
也可以通过scheduled_job()装饰器实现
```python
from apscheduler.scheduler import BlockingScheduler


@sched.scheduled_job('interval', id='my_job_id', hours=2)
def job_function():
    print("Hello World")
```
jitter振动参数，给每次触发添加一个随机浮动秒数，一般适用于多服务器，避免同时运行造成服务拥堵。
```python
# 每小时（上下浮动120秒区间内）运行`job_function`
sched.add_job(job_function, 'interval', hours=1, jitter=120)
```
### cron 强大的类crontab表达式
```python
# 注意参数顺序
class apscheduler.triggers.cron.CronTrigger(
year=None, 
month=None, 
day=None, 
week=None, 
day_of_week=None, 
hour=None, 
minute=None,
second=None, 
start_date=None, 
end_date=None, 
timezone=None, 
jitter=None)
```
当省略时间参数时，在显式指定参数之前的参数会被设定为*，之后的参数会被设定为最小值，week 和day_of_week的最小值为*。比如，设定day=1, minute=20等同于设定year='*', month='*', day=1, week='*', day_of_week='*', hour='*', minute=20, second=0，即每个月的第一天，且当分钟到达20时就触发。

### 表达式类型
|表达式|参数类型|描述|
|:---|:---|:---|
|*|所有|通配符。例：minutes=*即每分钟触发|
*/a|	所有|	可被a整除的通配符。
a-b	|所有|	范围a-b触发
a-b/c|	所有|	范围a-b，且可被c整除时触发
xth y|	日|	第几个星期几触发。x为第几个，y为星期几
last x|	日|	一个月中，最后个星期几触发
last|	日|	一个月最后一天触发
x,y,z|	所有|	组合表达式，可以组合确定值或上方的表达式
>注！month和day_of_week参数分别接受的是英语缩写jan– dec 和 mon – sun
```python
from apscheduler.schedulers.blocking import BlockingScheduler

def job_function():
    print "Hello World"

sched = BlockingScheduler()

# 任务会在6月、7月、8月、11月和12月的第三个周五，00:00、01:00、02:00和03:00触发
sched.add_job(job_function, 'cron', month='6-8,11-12', day='3rd fri', hour='0-3')

sched.start()
```
start_date 和 end_date 可以用来适用时间范围
```python
# 在2014-05-30 00:00:00前，每周一到每周五 5:30运行
sched.add_job(job_function, 'cron', day_of_week='mon-fri', hour=5, minute=30, end_date='2014-05-30')
```
通过 scheduled_job() 装饰器实现：
```python
@sched.scheduled_job('cron', id='my_job_id', day='last sun')
def some_decorated_task():
    print("I am printed at 00:00:00 on the last Sunday of every month!")
```
使用标准crontab表达式：
```python
sched.add_job(job_function, CronTrigger.from_crontab('0 0 1-15 may-aug *'))
```
也可以添加jitter振动参数
```python
# 每小时上下浮动120秒触发
sched.add_job(job_function, 'cron', hour='*', jitter=120)
```
夏令时问题
有些timezone时区可能会有夏令时的问题。这个可能导致令时切换时，任务不执行或任务执行两次。避免这个问题，可以使用UTC时间，或提前预知并规划好执行的问题。
```python
# 在Europe/Helsinki时区, 在三月最后一个周一就不会触发；在十月最后一个周一会触发两次
sched.add_job(job_function, 'cron', hour=3, minute=30)
```
### 配置调度器
APScheduler 有多种不同的配置方法，你可以选择直接传字典或传参的方式创建调度器；也可以先实例一个调度器对象，再添加配置信息。灵活的配置方式可以满足各种应用场景的需要。

整套的配置选项可以参考API文档BaseScheduler类。一些调度器子类可能有它们自己特有的配置选项，以及独立的任务储存器和执行器也可能有自己特有的配置选项，可以查阅API文档了解。

下面举一个例子，创建一个使用默认任务储存器和执行器的BackgroundScheduler：
```python
from apscheduler.schedulers.background import BackgroundScheduler

scheduler = BackgroundScheduler()

# 因为是非阻塞的后台调度器，所以程序会继续向下执行
```
这样就可以创建了一个后台调度器。这个调度器有一个名称为default的MemoryJobStore（内存任务储存器）和一个名称是default且最大线程是10的ThreadPoolExecutor（线程池执行器）。

假如你现在有这样的需求，两个任务储存器分别搭配两个执行器；同时，还要修改任务的默认参数；最后还要改时区。可以参考下面例子，它们是完全等价的。

名称为“mongo”的MongoDBJobStore
名称为“default”的SQLAlchemyJobStore
名称为“ThreadPoolExecutor ”的ThreadPoolExecutor，最大线程20个
名称“processpool”的ProcessPoolExecutor，最大进程5个
UTC时间作为调度器的时区
默认为新任务关闭合并模式（）
设置新任务的默认最大实例数为3
方法一：
```python
from pytz import utc

from apscheduler.schedulers.background import BackgroundScheduler
from apscheduler.jobstores.mongodb import MongoDBJobStore
from apscheduler.jobstores.sqlalchemy import SQLAlchemyJobStore
from apscheduler.executors.pool import ThreadPoolExecutor, ProcessPoolExecutor


jobstores = {
    'mongo': MongoDBJobStore(),
    'default': SQLAlchemyJobStore(url='sqlite:///jobs.sqlite')
}
executors = {
    'default': ThreadPoolExecutor(20),
    'processpool': ProcessPoolExecutor(5)
}
job_defaults = {
    'coalesce': False,
    'max_instances': 3
}
scheduler = BackgroundScheduler(jobstores=jobstores, executors=executors, job_defaults=job_defaults, timezone=utc)
```
方法二：
```python
from apscheduler.schedulers.background import BackgroundScheduler


# The "apscheduler." prefix is hard coded
scheduler = BackgroundScheduler({
    'apscheduler.jobstores.mongo': {
         'type': 'mongodb'
    },
    'apscheduler.jobstores.default': {
        'type': 'sqlalchemy',
        'url': 'sqlite:///jobs.sqlite'
    },
    'apscheduler.executors.default': {
        'class': 'apscheduler.executors.pool:ThreadPoolExecutor',
        'max_workers': '20'
    },
    'apscheduler.executors.processpool': {
        'type': 'processpool',
        'max_workers': '5'
    },
    'apscheduler.job_defaults.coalesce': 'false',
    'apscheduler.job_defaults.max_instances': '3',
    'apscheduler.timezone': 'UTC',
})
```
方法三：
```python
from pytz import utc

from apscheduler.schedulers.background import BackgroundScheduler
from apscheduler.jobstores.sqlalchemy import SQLAlchemyJobStore
from apscheduler.executors.pool import ProcessPoolExecutor


jobstores = {
    'mongo': {'type': 'mongodb'},
    'default': SQLAlchemyJobStore(url='sqlite:///jobs.sqlite')
}
executors = {
    'default': {'type': 'threadpool', 'max_workers': 20},
    'processpool': ProcessPoolExecutor(max_workers=5)
}
job_defaults = {
    'coalesce': False,
    'max_instances': 3
}
scheduler = BackgroundScheduler()

# ..这里可以添加任务

scheduler.configure(jobstores=jobstores, executors=executors, job_defaults=job_defaults, timezone=utc)
```
### 启动调度器
启动调度器是只需调用start()即可。除了BlockingScheduler，非阻塞调度器都会立即返回，可以继续运行之后的代码，比如添加任务等。

对于BlockingScheduler，程序则会阻塞在start()位置，所以，要运行的代码必须写在start()之前。

>注！调度器启动后，就不能修改配置了。

添加任务
添加任务的方法有两种：

通过调用add_job()
通过装饰器scheduled_job()
第一种方法是最常用的；第二种方法是最方便的，但缺点就是运行时，不能修改任务。第一种add_job()方法会返回一个apscheduler.job.Job实例，这样就可以在运行时，修改或删除任务。

在任何时候你都能配置任务。但是如果调度器还没有启动，此时添加任务，那么任务就处于一个暂存的状态。只有当调度器启动时，才会开始计算下次运行时间。

还有一点要注意，如果你的执行器或任务储存器是会序列化任务的，那么这些任务就必须符合：

回调函数必须全局可用
回调函数参数必须也是可以被序列化的
内置任务储存器中，只有MemoryJobStore不会序列化任务；内置执行器中，只有ProcessPoolExecutor会序列化任务。

>重要提醒！
如果在程序初始化时，是从数据库读取任务的，那么必须为每个任务定义一个明确的ID，并且使用replace_existing=True，否则每次重启程序，你都会得到一份新的任务拷贝，也就意味着任务的状态不会保存。

>建议
如果想要立刻运行任务，可以在添加任务时省略trigger参数

### 移除任务
如果想从调度器移除一个任务，那么你就要从相应的任务储存器中移除它，这样才算移除了。有两种方式：

调用remove_job()，参数为：任务ID，任务储存器名称
在通过add_job()创建的任务实例上调用remove()方法
第二种方式更方便，但前提必须在创建任务实例时，实例被保存在变量中。对于通过scheduled_job()创建的任务，只能选择第一种方式。

当任务调度结束时（比如，某个任务的触发器不再产生下次运行的时间），任务就会自动移除。
```python
job = scheduler.add_job(myfunc, 'interval', minutes=2)
job.remove()
```
同样，通过任务的具体ID：
```
scheduler.add_job(myfunc, 'interval', minutes=2, id='my_job_id')
scheduler.remove_job('my_job_id')
```
### 暂停和恢复任务
通过任务实例或调度器，就能暂停和恢复任务。如果一个任务被暂停了，那么该任务的下一次运行时间就会被移除。在恢复任务前，运行次数计数也不会被统计。

暂停任务，有以下两个方法：

+ apscheduler.job.Job.pause()  
+ apscheduler.schedulers.base.BaseScheduler.pause_job()
恢复任务，

+ apscheduler.job.Job.resume()
+ apscheduler.schedulers.base.BaseScheduler.resume_job()
### 获取任务列表
通过get_jobs()就可以获得一个可修改的任务列表。get_jobs()第二个参数可以指定任务储存器名称，那么就会获得对应任务储存器的任务列表。

print_jobs()可以快速打印格式化的任务列表，包含触发器，下次运行时间等信息。

修改任务
通过apscheduler.job.Job.modify()或modify_job()，你可以修改任务当中除了id的任何属性。

比如：
```python
job.modify(max_instances=6, name='Alternate name')
```
如果想要重新调度任务（就是改变触发器），你能通过apscheduler.job.Job.reschedule()或reschedule_job()来实现。这些方法会重新创建触发器，并重新计算下次运行时间。

比如：
```python
scheduler.reschedule_job('my_job_id', trigger='cron', minute='*/5')
```
### 关闭调度器
关闭方法如下：
```python
scheduler.shutdown()
```
默认情况下，调度器会先把正在执行的任务处理完，再关闭任务储存器和执行器。但是，如果你就直接关闭，你可以添加参数：
```python
scheduler.shutdown(wait=False)
```
上述方法不管有没有任务在执行，会强制关闭调度器。

### 暂停、恢复任务进程
调度器可以暂停正在执行的任务：
```python
scheduler.pause()
```
也可以恢复任务：
```python
scheduler.resume()
```
同时，也可以在调度器启动时，默认所有任务设为暂停状态。
```python
scheduler.start(paused=True)
```
### 限制任务执行的实例并行数
默认情况下，在同一时间，一个任务只允许一个执行中的实例在运行。比如说，一个任务是每5秒执行一次，但是这个任务在第一次执行的时候花了6秒，也就是说前一次任务还没执行完，后一次任务又触发了，由于默认一次只允许一个实例执行，所以第二次就丢失了。为了杜绝这种情况，可以在添加任务时，设置max_instances参数，为指定任务设置最大实例并行数。

### 丢失任务的执行与合并
有时，任务会由于一些问题没有被执行。最常见的情况就是，在数据库里的任务到了该执行的时间，但调度器被关闭了，那么这个任务就成了“哑弹任务”。错过执行时间后，调度器才打开了。这时，调度器会检查每个任务的misfire_grace_time参数int值，即哑弹上限，来确定是否还执行哑弹任务（这个参数可以全局设定的或者是为每个任务单独设定）。此时，一个哑弹任务，就可能会被连续执行多次。

但这就可能导致一个问题，有些哑弹任务实际上并不需要被执行多次。coalescing合并参数就能把一个多次的哑弹任务揉成一个一次的哑弹任务。也就是说，coalescing为True能把多个排队执行的同一个哑弹任务，变成一个，而不会触发哑弹事件。

注！如果是由于线程池/进程池满了导致的任务延迟，执行器就会跳过执行。要避免这个问题，可以添加进程或线程数来实现或把 misfire_grace_time值调高。

### 调度器事件
调度器允许添加事件侦听器。部分事件会有特有的信息，比如当前运行次数等。add_listener(callback,mask)中，第一个参数是回调对象，mask是指定侦听事件类型，mask参数也可以是逻辑组合。回调对象会有一个参数就是触发的事件。

具体可以查看文档中events模块，里面有关于事件类型以及事件参数的详细说明。
```python
def my_listener(event):
    if event.exception:
        print('The job crashed :(')
    else:
        print('The job worked :)')

# 当任务执行完或任务出错时，调用my_listener
scheduler.add_listener(my_listener, EVENT_JOB_EXECUTED | EVENT_JOB_ERROR)
```
### 事件类型
Constant|	Description	|Event class
|:---|:---|:---
EVENT_SCHEDULER_STARTED|	The scheduler was started|	SchedulerEvent
EVENT_SCHEDULER_SHUTDOWN|	The scheduler was shut down	|SchedulerEvent
EVENT_SCHEDULER_PAUSED|	Job processing in the scheduler was paused	|SchedulerEvent
EVENT_SCHEDULER_RESUMED|	Job processing in the scheduler was resumed	|SchedulerEvent
EVENT_EXECUTOR_ADDED|	An executor was added to the scheduler	|SchedulerEvent
EVENT_EXECUTOR_REMOVED|	An executor was removed to the scheduler	|SchedulerEvent
EVENT_JOBSTORE_ADDED|	A job store was added to the scheduler	|SchedulerEvent
EVENT_JOBSTORE_REMOVED|	A job store was removed from the scheduler	|SchedulerEvent
EVENT_ALL_JOBS_REMOVED|	All jobs were removed from either all job stores or one particular job store	|SchedulerEvent
EVENT_JOB_ADDED	|A job was added to a job store	|JobEvent
EVENT_JOB_REMOVED|	A job was removed from a job store	|JobEvent
EVENT_JOB_MODIFIED|	A job was modified from outside the scheduler	|JobEvent
EVENT_JOB_SUBMITTED|	A job was submitted to its executor to be run	|JobSubmissionEvent
EVENT_JOB_MAX_INSTANCES	|A job being submitted to its executor was not accepted by the executor because the job has already reached its maximum concurrently executing instances	|JobSubmissionEvent
EVENT_JOB_EXECUTED|	A job was executed successfully	|JobExecutionEvent
EVENT_JOB_ERROR|	A job raised an exception during execution	|JobExecutionEvent
EVENT_JOB_MISSED|	A job’s execution was missed	|JobExecutionEvent
EVENT_ALL|	A catch-all mask that includes every event type	|N/A
### 异常捕获
通过logging模块，可以添加apscheduler日志至DEBUG级别，这样就能捕获异常信息。

关于logging初始化的方式如下：
```python
import logging

logging.basicConfig()
logging.getLogger('apscheduler').setLevel(logging.DEBUG)
```
日志会提供很多调度器的内部运行信息。
