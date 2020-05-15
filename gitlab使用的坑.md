# 1.官网安装：
## 1.安装和配置必要的依赖项
```
sudo apt-get update
sudo apt-get install -y curl openssh-server ca-certificates
```
接下来，安装Postfix发送通知电子邮件。如果要使用其他解决方案发送电子邮件，请跳过此步骤并 在安装GitLab之后配置外部SMTP服务器。
```
sudo apt-get install -y postfix
```

在Postfix安装过程中，可能会出现一个配置屏幕。选择“ Internet网站”，然后按Enter。使用服务器的外部DNS作为“邮件名”，然后按Enter。如果出现其他屏幕，请继续按Enter接受默认设置。
## 2.添加GitLab软件包存储库并安装软件包
添加GitLab软件包存储库。
```
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
```
接下来，安装GitLab软件包。更改https://gitlab.example.com为您要访问GitLab实例的URL。安装将自动配置并在该URL上启动GitLab。

对于https://URL，GitLab将使用Let's Encrypt自动请求证书，这需要入站HTTP访问和有效的主机名。您也可以使用自己的证书或仅使用http：//。
```
sudo EXTERNAL_URL="https://gitlab.example.com" apt-get install gitlab-ee
```
## 3.浏览到主机名并登录
首次访问时，您将被重定向到密码重置屏幕。提供初始管理员帐户的密码，您将被重定向回登录屏幕。使用默认帐户的用户名root登录。

有关安装和配置的详细说明，请参见我们的文档。
Gitlab Community Edition 镜像使用帮助
注意: gitlab-ce 镜像仅支持 x86-64 架构

# 2.国内源安装（推荐）
版本：Debian/Ubuntu 用户
## 1.首先信任 GitLab 的 GPG 公钥:
```
curl https://packages.gitlab.com/gpg.key 2> /dev/null | sudo apt-key add - &>/dev/null
```
## 2.再选择你的 Debian/Ubuntu 版本
文本框中内容写进 
```
sudo vim /etc/apt/sources.list.d/gitlab-ce.list
```
你的Debian/Ubuntu版本:(18.04) ,然后将以下地址复制进去
```
deb https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu bionic main
```
## 3.安装 gitlab-ce:
```
sudo apt-get update
sudo apt-get install gitlab-ce
```
## 4.修改gitlab配置文件指定服务器ip和自定义端口：
```
vim  /etc/gitlab/gitlab.rb
```
进入编辑器后按“i”键进入编辑状态，ESC键退出编辑状态
修改以下信息：
```
external_url 'http://localhost:8899'
unicorn['port']=8088
```
退出并保存，命令输入“:wq”

ps:注意这里设置的端口不能被占用，默认是8080端口，如果8080已经使用，请自定义其它端口，并在防火墙设置开放相对应得端口
## 5.重置并启动GitLab
执行：
```
gitlab-ctl reconfigure
gitlab-ctl restart
```
提示  "ok: run:"表示启动成功
# 3.汉化
## 1.克隆版本库
```
cd /usr/local/src/
git clone https://gitlab.com/xhang/gitlab.git
```
## 2.获取当前Gitlab版本
```
gitlab_version=$(cat /opt/gitlab/embedded/service/gitlab-rails/VERSION)
```
## 3.比较汉化标签和原标签，导出patch用的diff文件
```
cd /usr/local/src/gitlab
git diff v${gitlab_version} v${gitlab_version}-zh > ../${gitlab_version}-zh.diff
```
## 4.先停止gitlab
```
gitlab-ctl stop
```
## 5.导入汉化补丁
```
patch -d /opt/gitlab/embedded/service/gitlab-rails -p1 < ../${gitlab_version}-zh.diff
```
PS：如果出现类似以下内容，则按住回车，一直跳过就行了
```
can't find file to patch at input line 5
Perhaps you used the wrong -p or --strip option?
The text leading up to this was:

--------------------------
|diff --git a/app/assets/javascripts/awards_handler.js b/app/assets/javascripts/awards_handler.js
|index eb0f06e..73e4833 100644
|--- a/app/assets/javascripts/awards_handler.js
|+++ b/app/assets/javascripts/awards_handler.js
--------------------------
File to patch:
```
## 6.然后启动gitlab
```
gitlab-ctl start
```
# 4.更改clone地址：
输入：
```
sudo vim /opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml
```
进入之后，更改
```yaml
host： 10.1.63.217
```
# 5.developer无法push
找到admin area，进入settings，进入repository，然后选择protected Branches 将allowed to merge 以及allowed to push中角色选择成developer
