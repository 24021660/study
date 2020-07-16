## 1.获取时间、时间戳
时间：`nowtime=$(date "+%Y%m%d")`   
时间戳：`nowtimestamp=$(date +%s)`
## 2.加减乘除
```sh
((i=100-10))
```
## 3.分割字符串
```sh
#!/bin/bash
 
string="hello,shell,split,test"  
array=(${string//,/ })  
#其中可以选择列表中的每一个数，分别为${a[0]},${a[1]}
for var in ${array[@]}
do
   echo $var
done 
```
## 4.字符串拼接
```sh
backtimestamp16=1
addstr=2
nowtimstampfinal=$backtimestamp16$addstr
```
## 5.10转16进制
```sh
backtimestamp16=$(expr $backtimestamp | awk '{printf "%x\n",$0}')
```

