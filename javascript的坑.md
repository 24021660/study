# 引用jquery
首先将jquery下载到本地,之后使用:
```html
<script src="js/jquery-3.4.1.min.js"></script>
```
将jquery引入前端使用.

# jquery的POST
下面是一个点击按钮触发的post案例:  
点击按钮向地址发送一个post,参数为`postclass`与`tel`,然后接收借口给的返回值`result`
```JavaScript
$('button').click(function(){
    $.post('http://192.168.1.102:8080/usermatchinfo', {postclass:'all',tel:"2345"},function(result){
        alert("Data"+result)
    });
});
```
# 修改html代码
函数：
`function add（）{  document.getbyid().或者$("#id").attr("value",123)     }`

# 在html代码中修改一段代码:
例如,在id为`id4`的标签项中进行修改
```JavaScript
html1='<div>12312321321</div>'
id="#id4"
$(id).html(html1)
```