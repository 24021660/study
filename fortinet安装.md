# fortinet ubuntu安装方法
## Ubuntu 18.04 LTS
### Install gpg key
`wget -O - https://repo.fortinet.com/repo/ubuntu/DEB-GPG-KEY | sudo apt-key add - `
### Add the following line in /etc/apt/sources.list
`deb [arch=amd64] https://repo.fortinet.com/repo/ubuntu/ /bionic multiverse `
### Update package lists
`sudo apt-get update`
### Install FortiClient
`sudo apt install forticlient`
