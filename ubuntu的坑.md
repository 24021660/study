# ubuntu
begin to learn new language and how to use Github
1.关于汉语拼音：搜狗网站搜linux然后进行安装，之后在右上角开关里面找到setting找到语言，然后选择进入manage installed langues，选择之后进入，在最下面选择input method :fcitx，然后重新注销，右上角出现键盘，进入configuration，然后进入之后添加语言，下面去掉仅从当前语言选择，然后选择最下面的搜狗语言就可以了。
2.关于ubuntu software中更新慢的问题，点击打开终端，分别输入以下代码：
      （1）原来数据文件改名并备份
      sudo mv /etc/apt/sources.list sources.list_backup
      （2）新建文件
      sudo vim /etc/apt/sources.list
      （3）更新镜像文件
      deb http://cn.archive.ubuntu.com/ubuntu/ xenial main restricted
      deb http://cn.archive.ubuntu.com/ubuntu/ xenial-updates main restricted
      deb http://cn.archive.ubuntu.com/ubuntu/ xenial universe
      deb http://cn.archive.ubuntu.com/ubuntu/ xenial-updates universe
      deb http://cn.archive.ubuntu.com/ubuntu/ xenial multiverse
      deb http://cn.archive.ubuntu.com/ubuntu/ xenial-updates multiverse
      deb http://cn.archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse
      deb http://security.ubuntu.com/ubuntu xenial-security main restricted
      deb http://security.ubuntu.com/ubuntu xenial-security universe
      deb http://security.ubuntu.com/ubuntu xenial-security multiverse
      （4）提交更新
      sudo apt-get update
