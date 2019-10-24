# 1.spark安装
## scala安装
因为spark是scala开发的，所以必须先安装scala
先下载scala：`wget http://distfiles.macports.org/scala2.11/scala-2.11.8.tgz`
解压缩：`tar xvf scala-2.11.8.tgz`
迁移到usr/local:`sudo mv scala-2.11.8 /usr/local/scala`
编辑~/.bashrc:`sudo vim ~/.bashrc`
输入以下指令：
```conf
#SCALA Varibles
export SCALA_HOME=/usr/local/scala
export PATH=$SCALA_HOME/bin
#SCALA Varibles
```
然后执行生效：`source ~/.bashrc`
测试：输入scala，进入之后输入1+1回车，会出现2，输入：q退出
## 安装spark


