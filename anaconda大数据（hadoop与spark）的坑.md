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

# 2.外网访问notebook
## （1）生成一个 notebook 配置文件
默认情况下，配置文件 ~/.jupyter/jupyter_notebook_config.py 并不存在，需要自行创建。使用下列命令生成配置文件：  
`jupyter notebook --generate-config`  
如果是 root 用户执行上面的命令，会发生一个问题：  
`Running as root it not recommended. Use --allow-root to bypass.`  
提示信息很明显，root 用户执行时需要加上 --allow-root 选项。  
`jupyter notebook --generate-config --allow-config`  
执行成功后，会出现下面的信息：  
`Writing default config to: /root/.jupyter/jupyter_notebook_config.py`  
## (2)生成密码
自动生成   
从 jupyter notebook 5.0 版本开始，提供了一个命令来设置密码：`jupyter notebook password`，生成的密码存储在 jupyter_notebook_config.json。  
```
$ jupyter notebook password
Enter password:  ****
Verify password: ****
[NotebookPasswordApp] Wrote hashed password to /Users/you/.jupyter/jupyter_notebook_config.json
```
## (3)修改配置文件
在 jupyter_notebook_config.py 中找到下面的行，取消注释并修改。
```conf
c.NotebookApp.ip='*'
c.NotebookApp.password = u'sha:ce...刚才复制的那个密文'
c.NotebookApp.open_browser = False
c.NotebookApp.port =8888 #可自行指定一个端口, 访问时使用该端口
```
以上设置完以后就可以在服务器上启动 jupyter notebook，jupyter notebook, root 用户使用 jupyter notebook --allow-root。打开 IP:指定的端口, 输入密码就可以访问了。  
需要注意的是不能在隐藏目录 (以 . 开头的目录)下启动 jupyter notebook, 否则无法正常访问文件。  
密码可以在生成的token中加入，新建密码