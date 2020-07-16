docker搭建方法：
`docker pull pypiserver/pypiserver`
下载完成后，使用htpasswd生成密码12345，并写入文件htpasswd.txt
pipserver:MTEWszYnI5ilw
然后运行即可：
```
sudo docker run -itd -p 8001:8080 -v ~/htpasswd.txt:/data/htpasswd.txt -v /home/packages:/data/packages pypiserver/pypiserver:latest -P htpasswd.txt packages

```
安装包：
pip3 download -r requirements.txt -i xxx -d /home/packages