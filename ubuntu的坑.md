# ubuntu
begin to learn new language and how to use Github
1.关于汉语拼音：搜狗网站搜linux然后进行安装，之后在右上角开关里面找到setting找到语言，然后选择进入manage installed langues，选择之后进入，在最下面选择input method :fcitx，然后重新注销，右上角出现键盘，进入configuration，然后进入之后添加语言，下面去掉仅从当前语言选择，然后选择最下面的搜狗语言就可以了。
2.关于ubuntu software中更新慢的问题，点击打开终端，分别输入以下代码：
      （1）原来数据文件改名并备份
      sudo mv /etc/apt/sources.list sources.list_backup
      （2）新建文件
      sudo vim /etc/apt/sources.list
      （3）更新镜像文件
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
      （4）提交更新
      sudo apt-get update
3.关于文件不用vim取得管理员权限的情况：打开： sudo gedit /etc/laptop-mode/laptop-mode.conf，回车后输入你的管理员密码，编辑完保存就可以了
4.关于拼音输入法fcitx在输入法选择里面没有的情况：sudo add-apt-repository ppa:fcitx-team/nightly 添加下载源，然后sudo apt-get update
最后sudo apt-get install fcitx就有了。
5.切换root：sudo su
6.创建快捷方式：输入vim pycharm.desktop 进入vim然后：
[Desktop Entry]
Name = PyCharm
Type = Application
Exec = /home/tony/pycharm-community/bin/pycharm.sh
Icon = /home/tony/pycharm-community/bin/pycharm.png
Terminal = False
保存之后右键，属性，点击允许程序打开的勾。
7.硬盘格式化:打开app菜单,搜索disks,进入,选择硬盘,邮件,format就行了
8.vmware的问题,虚拟机相当不好用,需要更新系统安装包之后,再尝试安装gcc,apt-get install gcc
      linuxidc@linuxidc:~$ wget -O ~/vmware.bin https://www.vmware.com/go/getWorkstation-linux
      sudo apt install build-essential
9.pycharm setting的配置,选择本地Python 结果出现了Python安装包python packaging tools not found找不到的问题,直接sudo apt install python3-pip之后再安装包就可以了.
10.sqlserver-pyodbc-azure安装不上sudo apt install unixodbc-dev
11.关于远程链接桌面:先sudo apt-get install rdesktop 然后rdesktop 39.106.189.206 ,注意如果出现ssp的错误要去windows远程桌面中去掉网络验证身份的勾
12.wine中文乱码解决:先创建zh.reg 然后regedit zh.reg
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
13.bundle安装，进入当前文件夹然后输入sudo ./*bundle进行安装之后完成。
