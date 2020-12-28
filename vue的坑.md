## 1.安装
目前使用npm安装比较好
前提先安装cnpm
安装完node与cnpm之后运行下面代码：

```sh
# 全局安装 vue-cli
$ cnpm install --global vue-cli
# 创建一个基于 webpack 模板的新项目
$ vue init webpack my-project
# 这里需要进行一些配置，默认回车即可
This will install Vue 2.x version of the template.

For Vue 1.x use: vue init webpack#1.0 my-project

? Project name my-project
? Project description A Vue.js project
? Author runoob <test@runoob.com>
? Vue build standalone
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Setup unit tests with Karma + Mocha? Yes
? Setup e2e tests with Nightwatch? Yes

   vue-cli · Generated "my-project".

   To get started:
   
     cd my-project
     npm install
     npm run dev
   
   Documentation can be found at https://vuejs-templates.github.io/webpack
```
安装完成并新建完项目之后，运行：
```sh
$ cd my-project
$ cnpm install
$ cnpm run dev

DONE  Compiled successfully in 4388ms

> Listening at http://localhost:8080
```
之后就可以访问网址了
## 2.目录结构

| 目录/文件 | 说明 |
| ----  |  ----  |
|build	|项目构建(webpack)相关代码
|config	|配置目录，包括端口号等。我们初学可以使用默认的。
|node_modules	|npm 加载的项目依赖模块
|src	|这里是我们要开发的目录，基本上要做的事情都在这个目录里。里面包含了几个目录及文件：assets: 放置一些图片，如logo等。components: 目录里面放了一个组件文件，可以不用。App.vue: 项目入口文件，我们也可以直接将组件写这里，而不使用 components 目录。main.js: 项目的核心文件。
|static	|静态资源目录，如图片、字体等。
|test	|初始测试目录，可删除
|.xxxx文件	|这些是一些配置文件，包括语法配置，git配置等。
|index.html	|首页入口文件，你可以添加一些 meta 信息或统计代码啥的。
|package.json	|项目配置文件。
|README.md	|项目的说明文档，markdown 格式
## 3.第一个示例
```html
<div id="vue_det">
    <h1>site : {{site}}</h1>
    <h1>url : {{url}}</h1>
    <h1>{{details()}}</h1> 
</div>
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue_det',  //代表元素id
        data: {
            site: "菜鸟教程",  //site赋值
            url: "www.runoob.com",  //url赋值
            alexa: "10000"  //alexa赋值
        },
        methods: {    //methods 用于定义的函数，可以通过 return 来返回函数值。这里定义了一个details的函数
            details: function() {
                return  this.site + " - 学的不仅是技术，更是梦想！";
            }
        }
    })
</script>
```
运行即可
## 4.vue语法
### 1.插值
#### （1）文本
数据绑定最常见的形式就是使用 {{...}}（双大括号）的文本插值：
```html
<div id="app">
  <p>{{ message }}</p>
</div>
```
#### （2）html
使用 v-html 指令用于输出 html 代码
```html
<div id="app">
    <div v-html="message"></div>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    message: '<h1>菜鸟教程</h1>'
  }
})
</script>
```
注意，这样样式在scoped中会丢失，为了不丢失所以需要以下操作：
```css
>>> .test{color:#d6d5d5}
加三个箭头
```
#### (3)样式
HTML 属性中的值应使用 v-bind 指令。
以下实例判断 use 的值，如果为 true 使用 class1 类的样式，否则不使用该类：
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
</head>
<style>
.class1{
  background: #444;
  color: #eee;
}
</style>
<body>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>

<div id="app">
  <label for="r1">修改颜色</label><input type="checkbox" v-model="use" id="r1">
  <br><br>
  <div v-bind:class="{'class1': use}">
    v-bind:class 指令
  </div>
</div>
    
<script>
new Vue({
    el: '#app',
  data:{
      use: false
  }
});
</script>
</body>
```
#### (4)表达式
Vue.js 都提供了完全的 JavaScript 表达式支持。
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
	{{5+5}}<br>
	{{ ok ? 'YES' : 'NO' }}<br>
	{{ message.split('').reverse().join('') }}
	<div v-bind:id="'list-' + id">菜鸟教程</div>
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
	ok: true,
    message: 'RUNOOB',
	id : 1
  }
})
</script>
</body>
</html>
```
### 2.指令
指令是带有 v- 前缀的特殊属性。
指令用于在表达式的值改变时，将某些行为应用到 DOM 上。如下例子：
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <p v-if="seen">现在你看到我了</p>
    <template v-if="ok">
      <h1>菜鸟教程</h1>
      <p>学的不仅是技术，更是梦想！</p>
      <p>哈哈哈，打字辛苦啊！！！</p>
    </template>
</div>
    
<script>
new Vue({
  el: '#app',
  data: {
    seen: false,
    ok: true
  }
})
</script>
</body>
</html>
```
#### 参数
参数在指令后以冒号指明。例如， v-bind 指令被用来响应地更新 HTML 属性
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <pre><a v-bind:href="url">菜鸟教程</a></pre>
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
    url: 'http://www.runoob.com'
  }
})
</script>
</body>
</html>
```
在这里 href 是参数，告知 v-bind 指令将该元素的 href 属性与表达式 url 的值绑定。
另一个例子是 v-on 指令，它用于监听 DOM 事件：
```html
<a v-on:click="doSomething">
```
在这里参数是监听的事件名.
#### 修饰符
修饰符是以半角句号 . 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。例如，.prevent 修饰符告诉 v-on 指令对于触发的事件调用 event.preventDefault()
```html
<form v-on:submit.prevent="onSubmit"></form>
```
### 3.用户输入
在 input 输入框中我们可以使用 v-model 指令来实现双向数据绑定：
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
    <p>{{ message }}</p>
    <input v-model="message">
</div>
	
<script>
new Vue({
  el: '#app',
  data: {
    message: 'Runoob!'
  }
})
</script>
</body>
</html>
```
`v-model` 指令用来在 input、select、textarea、checkbox、radio 等表单控件元素上创建双向数据绑定，根据表单上的值，自动更新绑定的元素的值。
按钮的事件我们可以使用 v-on 监听事件，并对用户的输入进行响应。
以下实例在用户点击按钮后对字符串进行反转操作：
### 3.在webpack中使用vue
首先使用的是main.js,为入口函数，然后通过导入并渲染一个vue文件来更改html
```es6
//hello.vue
<template>
<div id="app">
{{message}}
<div>
</template>
<script>
export default {
  data(){
    return{
      message:"helloworld"
    }
  }
  methods:{
    mes(){
      console.log('这是一个测试')
    }
  }

}
</script>

//main.js
import hello from './hello.vue'
import Vue from 'vue'

new Vue({
  el:"#app",
  render c=>c(hello)
})

```
### （2）export与export default
```es6
//export default 一个js模块中只允许使用一次。  
//但是可以同时使用export 以及export default  
//使用export向外暴露成员只能用{}来引用，且export导入时必须同名，并且可以导入导出多个成员，可以按需接收
export default test //a.js
import a from 'a.js'
export {test1,test2} //b.js
import {test1} from 'b.js'  
```
### (3)router使用
使用路由
```es6
//main.js
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(VueRouter)

import app from './App.vue'

var router=new VueRouter({
  routes:[
    {path:'/account',compoment:account},
    {path:'/goodlist',compoment:goodlist}
  ]})
var vm =Vue({
  el:"#app",
  render:c=>c(app),
  router
})

//App.vue
<template>
<div id="app">
<router-link to="/account"><router-link>
<router-link to="/goodlist"><router-link>
<router-view><router-view>
</div>
</template>
<script></script>
<sytle></style>

//goodlisthe

```
抽离路由文件,新建router.js
```js
import VueRouter from 'vue-router'
import account from './test/account.vue'
import goodlist from './test/goodlist.vue'


routes={[{path:'/account',compoment:account},{path:'/goodlist',compoment:goodlist}]}
var router=new VueRouter(routes)
export default router
```
然后使用main.js导入即可
```js
import Vue from 'vue'
import app from './App.vue'
import VueRouter from 'vue-router'
import router from './router.js'
//先use
Vue.use(VueRouter)
//然后在new Vue中加入router
var vm= new Vue({
  el:"#app",
  router,
  render: c=>c(app)
})

```
## 页面内子页面跳转：
需要设定子路由：
```js
var routes=[{
  pages:'/123',
  components:123,
  children:[{
    pages:'234',components:234}]}]
//这样就可以实现页面内跳转，linkto的时候要写上123/234才行
```
