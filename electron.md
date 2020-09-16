## 1.安装
下载node，然后安装完成之后
在终端输入`node -v`,`npm -v`分别查看版本号。
## 2.第一个实例
创建一个新文件夹，名称为test  
然后使用`npm init`  
然后在该文件夹中输入`npm install --save-dev electron`  
如果想要通过`cnpm start`来执行的话需要在文件夹的`packages.json`里面写入start的相关配置：
```json
{
  "name": "your-app",
  "version": "0.1.0",
  "main": "main.js", //改成启动用的js，我这里是main.js
 //下面是启动的配置项，需要添加进去
  "scripts": {
    "start": "electron ."
  }
}
```
然后添加`main.js`:
```js
const { app, BrowserWindow } = require('electron')

function createWindow () {   
  // 创建浏览器窗口
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true
    }
  })

  // 并且为你的应用加载index.html
  win.loadFile('index.html')

  // 打开开发者工具
  win.webContents.openDevTools()
}

// Electron会在初始化完成并且准备好创建浏览器窗口时调用这个方法
// 部分 API 在 ready 事件触发后才能使用。
app.whenReady().then(createWindow)

// Quit when all windows are closed, except on macOS. There, it's common
// for applications and their menu bar to stay active until the user quits
// explicitly with Cmd + Q.
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('activate', () => {
  // On macOS it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  if (BrowserWindow.getAllWindows().length === 0) {
    createWindow()
  }
})

// 您可以把应用程序其他的流程写在在此文件中
// 代码 也可以拆分成几个文件，然后用 require 导入。
```
再添加一个主页index.html：
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
    <!-- https://electronjs.org/docs/tutorial/security#csp-meta-tag -->
    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
  </head>
  <body>
    <h1>Hello World!</h1>
    We are using node <script>document.write(process.versions.node)</script>,
    Chrome <script>document.write(process.versions.chrome)</script>,
    and Electron <script>document.write(process.versions.electron)</script>.
  </body>
</html>
```
然后确保`packages.json`,`index.html`,`main.js`存在，运行`cnpm install`,`cnpm start`即可打开自己的实例
## 3.与vue结合
安装vue-cli3:`cnpm install -g @vue/cli`  
然后新建一个vue项目：`vue create test`  
添加electron:`vue add electron-builder`  
然后执行命令就可以看了：`cnpm run electron:serve`  
同时也可以开启网页模式：`cnpm run serve`
## 4.窗口
### （1）窗口各项属性
|属性名称|说明|
|-|----|
|x,y,center,movable|分别代表窗口启动时的位置|
|width,height,  minWidth,minHeight,  maxWidth,maxHeight,  resizable,minimizable,  maxmizable|可以设置窗口大小以及是否允许用户改变窗口大小|
|title,icon,frame,autoHideMenuBar,titleBarSytle|设置窗口的边框/标题栏与菜单栏|
|nodeIntegration,nodeIntegrationInWorker,  nodeIntegrationInSubFrames|控制窗口加载的网页是否集成node.js环境|
|preload,webSecurity,contextIsolation|允许开发者最大限度的控制渲染进程加载的页面|
### (2)标题栏更改：
关于electron的`main.js`文件主要对应与vue中是`background.js`
目前多数流行是需要隐藏标题栏的
```js
win=new BrowserWindow({
    frame:false,
    webPreferences:{nodeIntegration:true}
});
```
但是还需要拖拽，最大最小关闭等按钮。
需要进入`src/App.vue`修改template中的代码为如下代码：
```html
<template>
  <div id="app">
    <div class="titleBar">
      <div class="title">
        <div class="logo"><img src="@/assets/logo.png"></div>
        <div class="txt">窗口标题</div>
      </div>
      <div class="windowTool">
        <div @click="minisize"><i class="iconfont iconminisize"></i></div>
        <div v-if="minisize" @click="restore"><i class="iconfont iconrestore"></i></div>
        <div v-else @click="maxsize"><i class="iconfont iconmaxsize"></i></div>
        <div @click="close"><i class="iconfont iconclose"></i></div>
      </div>
    </div>
    <div class="content">
      <router-view>
      </router-view>
    </div>
  </div>
</template>
```
然后修改整体样式：
```css
<style>
  body,
  html {
    margin: 0px;
    padding: 0px;
    overflow: hidden;
    height: 100%;
  }

  #app {
    text-align: center;
    margin: 0px;
    padding: 0px;
    height: 100%;
    overflow: hidden;
    box-sizing: border-box;
    border: 1px solid #f5222d;
    display: flex;
    flex-direction: column;
  }
</style>
```
最后修改细节：
```scss
<style lang="scss" scoped>
  @import url(https://at.alicdn.com/t/font_1378132_s4e44adve5.css);

  .titleBar {
    height: 38px;
    line-height: 36px;
    background: #fff1f0;
    display: flex;
    border-bottom: 1px solid #f5222d;

    .title {
      flex: 1;
      display: flex;
      -webkit-app-region: drag;

      .logo {
        padding-left: 8px;
        padding-right: 6px;
        img{width: 20px;height: 20px;margin-top: 7px;}
      }
      .txt {text-align:left;flex:1;}
    }
    .windowtool{
      div{color:#888;height:100%;width:38px;display: inline-block;cursor:pointer;
      i {font-size: 12px;}&:hover{background: #ffccc7}}
      .close:hover{color:#fff;background:#ff4d4f;}
    }
  }
  .content{flex:1;overflow-y: auto;overflow-x:auto;}
</style>
```
### (3)窗口控制按钮











