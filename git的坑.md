# vscode：解决操作git总让输入用户名及密码问题
　　只要是使用git操作，不管是同步，拉去，克隆，vscode总让我们输入用户名及密码，是一件很繁琐的事情 

　　

　　我们打开终端，会看到cmd定位在我们仓库位置，我们只要添加：git config --global credential.helper store

　　

　　再退出vscode并重启，进行git操作，就发现，vscode不再让我们每次都输入用户名和密码啦！

　　以上

　　END

# git仓库：
新建一个文件夹，cd进入 `git init --bare project.git`  
比如工程名称叫做`project`，就起名`project.git`  
然后在该目录输入`ll project.git/`  
之后输入`sudo chmod 777 project.git/ -R`  
将权限放开
之后就可以使用了,地址：
`ssh://backcoder@39.106.189.206/home/git/wangning.git`

# git 权限管理
chown runoob:runoobgroup file1.txt
