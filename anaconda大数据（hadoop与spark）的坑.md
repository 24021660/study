# 1.在notebook上使用spark
## 下载：
清华的源：`https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.3.1-Linux-x86_64.sh`  
安装：`sudo bash Anaconda3-5.3.1-Linux-x86_64.sh -b`  
配置：`sudo vim ~/.bashrc`  
```conf
export PATH=$PATH:/home/tony/anaconda3/bin
export ANACONDA_PATH=/home/tony/anaconda3
export PYSPARK_DRIVER_PYTHON=$ANACONDA_PATH/bin/ipython
export PYSPARK_PYTHON=$ANACONDA_PATH/bin/python
```
生效：`source ~/.bashrc`  
新建一个工作文件夹：  
`mkdir -p ~/pythonwork/ipynotebook cd ~/pythonwork/ipynotebook`  
加载pyspark的notebook：  
`PYSPARK_DRIVER_PYTHON=ipython PYSPARK_DRIVER_PYTHON_OPTS="notebook" pyspark`  
打开notebook：`http://localhost:8888`  

## 运行在Hadoop yarn-client
`k'PYSPARK_DRIVER_PYTHON=ipython PYSPARK_DRIVER_PYTHON_OPTS="notebook" HADOOP_CONF_IR=/usr/local/hadoop/etc/hadoop MASTER=yarn-client pyspark`
