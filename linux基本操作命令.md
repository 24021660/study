# init切换模式
```
init 3  #0 3 5 6
```
# echo显示
## 参数
>-E  不支持解释功能  
-n  不自动换行  
-e  带参数：
>>`\a` 警告声   
 `\b` 退格  
 `\c` 不加换行  
 `\n` 换行且移至行首  
\v
## 示例 
```shell
echo shell   #打印字符shell
echo $shell  #查看变量路径
echo -e "\a" #发出警报
```
# type
```shell
type echo #查看命令是内部命令还是外部命令
```

