虚拟环境配置
新建
新建文件夹D:\django_fateli_pyvenv，进入该目录，启动cmd

　　python -m venv .  (注意最后面是点，表示当前目录)
激活虚拟环境：进入Scripts目录，执行active.bat
退出虚拟环境，执行deactivate.bat

机器1：
1.首先在cmd命令行下，激活虚拟环境
 
 


 
2.在项目目录下新建一个whls文件夹(用来存储我们依赖包)然后在虚拟环境cmd命令下切换到whls目录
执行 pip freeze --all > requirements.txt 命 令 将安装包版本信息导入到requireents.txt文件中(该文件位置在执行cmd命令当前目录下，也就是whls下); 注意--all参数，加上此参数会将setuptools、urllib3包进行打包；如果不加，这两个不会打包。
 
 


 
3.机器2无网的情况下，先在机器1上将包下载到whls目录下－－(有网也可以用此方法，这样可以减少在机器2下载安装包时间)
whls目录下执行虚拟环境cmd命令pip install --download . -r requirements.txt （注意--download后边的点，代表是下载到当前目录，并且requirements.txt存在于当前目录中，否则会报错）

 

 
机器2：
1.新建虚拟环境，并将项目目录放入该虚拟环境中
2.激活虚拟环境

 

3.虚拟环境cmd命令进入机器1复制过来的项目下whls目录
4.执行命令安装包
前提是依赖第3步
1)有网情况通过requirements.txt包信息安装包命令如下：
pip install -r requirements.txt #安装依赖包
2)通过机器1下载的包安装；命令如下：
pip install --no-index --find-index= . -r requirements.txt 
注意命令中的.点 代表是当前目录也就是whls目录；是下载的包和
requirements.txt所在目录 
以上步骤完成后，就可以正常在机器2使用虚拟环境了。


对于Python开发用户来讲，PIP安装软件包是家常便饭。但国外的源下载速度实在太慢，浪费时间。而且经常出现下载后安装出错问题。所以把PIP安装源替换成国内镜像，可以大幅提升下载速度，还可以提高安装成功率。

国内源：
新版ubuntu要求使用https源，要注意。

清华：https://pypi.tuna.tsinghua.edu.cn/simple

阿里云：http://mirrors.aliyun.com/pypi/simple/

中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/

华中理工大学：http://pypi.hustunique.com/

山东理工大学：http://pypi.sdutlinux.org/ 

豆瓣：http://pypi.douban.com/simple/

临时使用：
可以在使用pip的时候加参数-i https://pypi.tuna.tsinghua.edu.cn/simple

例如：pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pyspider，这样就会从清华这边的镜像去安装pyspider库。
 
永久修改，一劳永逸：
Linux下，修改 ~/.pip/pip.conf (没有就创建一个文件夹及文件。文件夹要加“.”，表示是隐藏文件夹)

内容如下：

[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=mirrors.aliyun.com
windows下，直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini。内容同上。