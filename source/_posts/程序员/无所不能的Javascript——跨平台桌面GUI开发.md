---
title: Javascript开发跨平台桌面app
date: 2016-06-23 23:32:17
tags: [JavaScript]
description: develop cross-platform GUI app with electron
categories: 程序员
---
electron原本是atom shell的项目，由于集成node丰富的本地系统级API，
提供了与操作系统交互的功能，所以归结起来，现在javascrpit的技术栈之所以如此宽广，
要得益于NodeJS将其中浏览器的环境中分离出来

不过需要特别说明的是，这里生成的桌面应用，
实际上是Electron生成的一个由Javascrpit控制管理的迷你浏览器Chromeinum，
它其实是一个Chrome浏览器的试验版本，当然用的也是V8的内核
<!--more-->
不管怎么说，这对于喜欢前端，熟悉javascript的同学来说，
能够用前端和一点后台的知识去接触，尝试各种不同的领域，实在是不要太爽

Electron的入门也符合前端知识领域的特点，几乎没有门槛，
只要你有一点JS和Node的基础就行了，开发目录的文件层次结构也很简单，
后面会有打包工具帮助我们一键打包生成层次比较复杂的项目文件，
作为桌面GUI应用，当然也包括.exe的启动文件，

让我们看看如何开发一个桌面应用，或者说一个能和操作本地系统的web页面的基本方法~
你可以跟着步骤完成一些基本的文件，也可以直接下载[快速开始的demo](https://github.com/shudery/electron/archive/master.zip)
基本的文件层次很简单，用app存放一个具体的桌面应用，具体结构是这样的

![](https://raw.githubusercontent.com/shudery/public/master/Pictures/article/GUI_1.png)

OutApp用来存放打包输出的exe文件
至于为什么有两个``package.json``文件，后面打包操作时你就知道了

### 1、首先安装electron
命令行进入根目录，先用npm安装electron，如果还没有就先[安装node](https://nodejs.org/en/)
```
npm install -g electron-prebuilt
```
### 2、生成``package.json``配置文件
运行``npm init`` 生成一个``package.json``文件来存放应用的相关配置，这个是第一个``package.json``文件，位于外层根目录下的，运行 
```
npm install --save-dev electron-prebuilt  
```
会在devDependencies中生成依赖信息，方便后面打包
```
{
  "name": "firstGUI",
  "version": "1.0.0",
  "main": "app/main.js",//js入口文件
  "scripts": {
    "build": "electron-packager ./app firstApp --platform=win32 --arch=x64 --out ./OutApp --version 0.37.3 --overwrite --icon=./app/img/daguo. jpg",
    //打包命令，后面打包可以简化代码
  },
  "devDependencies": {
    "electron-prebuilt": "^1.2.0"
  }
}
```
### 3、新建main.js
生成一个入口的js文件来作为控制GUI窗口的主进程程序
```
const electron = require('electron');
// 控制应用生命周期的模块
const {app} = electron;
// 创建本地浏览器窗口的模块
const {BrowserWindow} = electron;
// 指向窗口对象的一个全局引用
let win;

function createWindow() {

 // 创建一个新的浏览器窗口
  win = new BrowserWindow({ width: 360, height: 572 });
 // 并且装载应用的index.html页面,注意路径
  win.loadURL('file://'+__dirname+'/html/index.html');
 // 当窗口关闭时调用的方法

  win.on('closed', () => {
  // 解除窗口对象的引用，通常而言如果应用支持多个窗口的话，你会在一个数组里
  // 存放窗口对象，在窗口关闭的时候应当删除相应的元素。
  win = null;
  });
}

// 当Electron完成初始化并且已经创建了浏览器窗口，则该方法将会被调用。
// 有些API只能在该事件发生后才能被使用。
app.on('ready', createWindow);

// 当所有的窗口被关闭后退出应用
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
});
app.on('activate', () => {
  if (win === null) {
    createWindow();
  }
});
// 在这个文件后面你可以直接包含你应用特定的由主进程运行的代码。
// 也可以把这些代码放在另一个文件中然后在这里导入。
```
### 4、index.html
GUI窗口（web页面）渲染的Html文件
```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>firstGUI</title>
  </head>
  <body>
    <h1 style=>Hello World!</h1>
  </body>
</html>
```

### 5、运行桌面app
大公告成，没错就是如此之快，运行``electron .``（跟一个空格和小点），或者``electron app/main``
打开制作好的GUI界面，so cool~

![](https://raw.githubusercontent.com/shudery/public/master/Pictures/article/GUI_2.png)

### 6、进行打包
既然是桌面应用，那肯定是要一键运行的，不需要安装什么依赖模块，
也不需要运行命令行程序的，所以为了提升逼格，让更多的人可以方便实用你的桌面app，
需要对app文件夹进行打包，安装打包程序 ``electron-packager``
```
npm install --save-dev electron-packager
```
打包的基本命令是 
```
electron-packager <location of project> <name of project> <platform> <architecture> <electron version><optional options>
```
上面已经在``package.json``的script里配置了简化的命令，可以根据自身情况对名称，操作系统，应用图标进行修改，
注意啦开始打包前，一定要复制一份``package.json``到app文件，
前面说了桌面应用不用下载依赖，其实是因为打包的时候就将依赖一同打包到文件包里了，
所以我们要打包app文件夹下的文件，需要一个``package.json``  来说明依赖项，注意修改里面的路径，要往下一级，然后运行``npm packager``

![](https://raw.githubusercontent.com/shudery/public/master/Pictures/article/GUI_3.png)

完成打包后可以看到OutApp里头已经有了相应的文件，
运行里面的exe文件会生成和本地测试时一样的GUI窗口~


一个简单的基于前端技术栈的桌面app算是完成了，后续的asar文件加密，
用nsis制作一个安装引导等等~有兴趣的可以继续完善~

一个桌面级app当然不是用来显示``hello world``的，那样和web页面有什么区别，
这里只是我入门的一个学习记录，一个基于electron的桌面应用
还有着非常丰富的与系统交互功能可以设计和开发~
更加详细的用法和配置有兴趣的同学不妨看看官方的[api演示文档](http://electron.atom.io/)，
它也是一个基于electron做的桌面app

