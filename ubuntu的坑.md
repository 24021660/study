# ubuntu
## 1.关于汉语拼音：
搜狗网站搜linux然后进行安装，之后在右上角开关里面找到setting找到语言，然后选择进入`manage installed langues`，选择之后进入，在最下面选择`input method :fcitx`，然后重新注销，右上角出现键盘，进入`configuration`，然后进入之后添加语言，下面去掉仅从当前语言选择，然后选择最下面的搜狗语言就可以了。  
## 2.关于ubuntu software中更新慢的问题
点击打开终端，分别输入以下代码：  
      （1）原来数据文件改名并备份 
      `sudo mv /etc/apt/sources.list sources.list_backup`  
      （2）新建文件 
     ` sudo vim /etc/apt/sources.list`  
      （3）更新镜像文件
```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```
（4）提交更新
     ` sudo apt-get update`  
## 3.关于文件不用vim取得管理员权限的情况
打开： `sudo gedit /etc/laptop-mode/laptop-mode.conf`，回车后输入你的管理员密码，编辑完保存就可以了    
## 4.关于拼音输入法fcitx在输入法选择里面没有的情况：
`sudo add-apt-repository ppa:fcitx-team/nightly` 添加下载源，然后`sudo apt-get update`
最后`sudo apt-get install fcitx`就有了。  
## 5.切换root：
`sudo su`
## 6.创建快捷方式：
输入`vim pycharm.desktop `进入vim然后：
```
[Desktop Entry]
Name = PyCharm
Type = Application
Exec = /home/tony/pycharm-community/bin/pycharm.sh
Icon = /home/tony/pycharm-community/bin/pycharm.png
Terminal = False
```
保存之后右键，属性，点击允许程序打开的勾。  
## 7.硬盘格式化:
打开app菜单,搜索`disks`,进入,选择`硬盘`,`右键`,`format`就行了  
8.vmware的问题
虚拟机相当不好用,需要更新系统安装包之后,再尝试安装`gcc,apt-get install gcc
      linuxidc@linuxidc:~$ wget -O ~/vmware.bin https://www.vmware.com/go/getWorkstation-linux
      sudo apt install build-essential`
## 9.pycharm setting的配置
选择本地Python 结果出现了Python安装包python packaging tools not found找不到的问题,直接`sudo apt install python3-pip`之后再安装包就可以了.
## 10.sqlserver-pyodbc-azure安装不上
`sudo apt install unixodbc-dev`
## 11.关于远程链接桌面:
先`sudo apt-get install rdesktop`   
然后`rdesktop 39.106.189.206 `,  
注意如果出现ssp的错误要去windows远程桌面中去掉网络验证身份的勾
## 12.wine中文乱码解决:
先创建zh.reg   
然后regedit zh.reg
```
      REGEDIT4
　　[HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\FontSubstitutes]
　　"Arial"="simsun"
　　"Arial CE,238"="simsun"
　　"Arial CYR,204"="simsun"
　　"Arial Greek,161"="simsun"
　　"Arial TUR,162"="simsun"
　　"Courier New"="simsun"
　　"Courier New CE,238"="simsun"
　　"Courier New CYR,204"="simsun"
　　"Courier New Greek,161"="simsun"
　　"Courier New TUR,162"="simsun"
　　"FixedSys"="simsun"
　　"Helv"="simsun"
　　"Helvetica"="simsun"
　　"MS Sans Serif"="simsun"
　　"MS Shell Dlg"="simsun"
　　"MS Shell Dlg 2"="simsun"
　　"System"="simsun"
　　"Tahoma"="simsun"
　　"Times"="simsun"
　　"Times New Roman CE,238"="simsun"
　　"Times New Roman CYR,204"="simsun"
　　"Times New Roman Greek,161"="simsun"
　　"Times New Roman TUR,162"="simsun"
　　"Tms Rmn"="simsun"
```
## 13.bundle安装
进入当前文件夹然后输入`sudo ./*bundle`进行安装之后完成。
## 14.ssh上传下载：
`root@39.106.189.206`
在linux下一般用scp这个命令来通过ssh传输文件。   
1、从服务器上下载文件  
`scp username@servername:/path/filename /var/www/local_dir`（本地目录）
 例如`scp root@192.168.0.101:/var/www/test.txt ` 把192.168.0.101上的/var/www/test.txt 的文件下载到/var/www/local_dir（本地目录）  
2、上传本地文件到服务器  
`scp /path/filename username@servername:/path   `  
例如`scp /var/www/test.php  root@192.168.0.101:/var/www/ ` 把本机/var/www/目录下的test.php文件上传到192.168.0.101这台服务器上的/var/www/目录中  
 3、从服务器下载整个目录  
`scp -r username@servername:/var/www/remote_dir/（远程目录） /var/www/local_dir`（本地目录）  
例如:`scp -r root@192.168.0.101:/var/www/test  /var/www/  `  
4、上传目录到服务器  
`scp  -r local_dir username@servername:remote_dir`
例如：`scp -r test  root@192.168.0.101:/var/www/`    
 把当前目录下的test目录上传到服务器的/var/www/ 目录
## 15.相关软件
pycharm：`sudo snap install [pycharm-professional|pycharm-community] --classic`
vscode:
mongodb:
mysql:
```
#命令1
sudo apt-get update
#命令2
sudo apt-get install mysql-server
```
nginx:

# Some index files failed to download. They have been ignored, or old ones used instead
vi /etc/resolv.conf
添加： nameserver 8.8.8.8 

# 安装所有驱动
ubuntu-drivers devices
sudo ubuntu-drivers autoinstall

# 查看端口占用


# 时间同步
sudo apt-get install ntpdate
2.将系统时间与网络同步

ntpdate cn.pool.ntp.org