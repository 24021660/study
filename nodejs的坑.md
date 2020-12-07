## 1.nodejs安装
官网下载，安装
安装之后，查看node版本：终端输入`node -v`
## 2.helloworld
新建helloworld.js写入：
```js
console.log('helloworld');
```
然后终端运行：输入`node helloworld.js`
正常的话，会在终端出现helloworld
## 3.创建第一个应用
新建一个server.js的文件，并输入：
```js
var http=require('http');
http.createServer(function(request,response){

    // 发送 HTTP 头部 
    // HTTP 状态值: 200 : OK
    // 内容类型: text/plain
    response.writeHead(200,{'Content-Type': 'text/plain'});
    
    // 发送响应数据 "Hello World"
    response.end('helloworld\n')};
    ).listen(8888);
// 终端打印如下信息
console.log('server is start:8888')
```
然后运行`node server.js`即可拉起本地相应端口的网站，进去就可以看到helloworld
## 4.创建路由
首先创建一个server.js
```js
var http = require("http");
var url = require("url");
 
function start(route) {
  function onRequest(request, response) {
    var pathname = url.parse(request.url).pathname;
    console.log("Request for " + pathname + " received.");
 
    route(pathname);
 
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello World");
    response.end();
  }
 
  http.createServer(onRequest).listen(8888);
  console.log("Server has started.");
}
 
exports.start = start;
```
然后创建一个router.js
```js
function route(pathname) {
  console.log("About to route a request for " + pathname);
}
 
exports.route = route;

```
使用的文件index.js
```js
var server = require("./server");
var router = require("./router");
 
server.start(router.route);
```
然后运行
```
node index.js
```
## 5.创建web服务器
新建一个server.js
```js
var http = require('http');
var fs = require('fs');
var url = require('url');
 
 
// 创建服务器
http.createServer( function (request, response) {  
   // 解析请求，包括文件名
   var pathname = url.parse(request.url).pathname;
   
   // 输出请求的文件名
   console.log("Request for " + pathname + " received.");
   
   // 从文件系统中读取请求的文件内容
   fs.readFile(pathname.substr(1), function (err, data) {
      if (err) {
         console.log(err);
         // HTTP 状态码: 404 : NOT FOUND
         // Content Type: text/html
         response.writeHead(404, {'Content-Type': 'text/html'});
      }else{             
         // HTTP 状态码: 200 : OK
         // Content Type: text/html
         response.writeHead(200, {'Content-Type': 'text/html'});    
         
         // 响应文件内容
         response.write(data.toString());        
      }
      //  发送响应数据
      response.end();
   });   
}).listen(8080);
 
// 控制台会输出以下信息
console.log('Server running at http://127.0.0.1:8080/');
```
然后在同文件夹下新建一个文件：index.html
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<body>
    <h1>我的第一个标题</h1>
    <p>我的第一个段落。</p>
</body>
</html>
```
然后运行就可以了


### fs使用：
1.新增文件：
```js
fs.writeFile(app.getAppPath()+'/kaoqin_info.txt', info_data, (err) => {
      if (err) throw err;
      console.log('文件已被保存');
    });
```
2.读取文件
```js
fs.readFile(app.getAppPath()+'/vpn_info.txt', 'utf-8',(err, data) => {
      if (err) throw err;
      event.reply('read-userinfo-response', data)
    });
```
3.删除文件：
```js

```