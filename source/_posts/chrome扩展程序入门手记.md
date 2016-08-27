---
title: chrome扩展程序入门手记
date: 2016-06-14 23:08:15
tags: [javascript,chrome]
description: share my record after learning how to build a plugin for chrome.
---
chrome是我非常喜欢的浏览器，它除了速度飞快，对前端代码支持友好的优点外，
还有非常丰富的扩展程序资源，提供了大量方便强大的web页面应用插件
这两天由于朋友的业务需求，接触了一些chrome扩展程序(即俗称插件)的有关代码
基于对谷歌开发者文档的学习，随手记录了写chrome插件的基本方法~
<!--more-->
每一个基本的Chrome插件,首先都有一个manifest.json
的配置文件用来存储插件的基本信息 

``
"manifest_version": 2,
  "name": "Getting started example",
  "description": "This extension shows a Google Image search result for the current page",
  "version": "1.0"
``

包括manifest的版本，扩展插件的名字，描述，版本

弹窗页面的信息browser_action, 在里面配置一个插件弹窗html页面代码，一般名字是popup.html，
浏览器界面显示的扩展图标位置，以及鼠标hover的title

``
"browser_action": {
    "default_icon": "images/icon.png",
    "default_popup": "popup.html",
    "default_title": "Click here!"
  }
``

可选的页面注入代码，一般名字是contentscript.js，
可选多个文件，匹配页面的matches，只对匹配到的注入代码

``
"content_scripts": [{
    "js": ["jquery.min.js", "contentscript.js"],
    "matches": ["http://*/*","https://*/*"]
  }]
``

还有插件要求的权限和要使用该插件的网页的匹配

``
"permissions": [
    "tabs",
    "http://*/"
  ]
``

关于manifest的更多字段配置信息，可以查看360翻译谷歌的[开发者文档](http://open.chrome.360.cn/extension_dev/manifest.html)

从上面的配置可以看到，一个简单插件除了有manifest之外
还有一个browser_action, 即popup.html来渲染点击插件之后的弹窗页面，
这个页面可以分离出来popup.js popup.css等文件，
这些文件可以放在同一个根目录下，文件中的引用跟普通项目一致可以取相对路径


如果要实现与浏览器打开页面的交互就必须还有一个注入脚本文件，
一般取名contentscript.js，弹窗页面popup page通过相应api实现和注入脚本的交互
再由注入脚本实现对web page的交互
在popup page（popup.js）中的输入代码

```
chrome.tabs.query({
      active: true
    }, function(tab) {
       var data = '';
        //要传递给注入脚本文件的信息
      chrome.tabs.sendMessage(tab[0].id, 'hello, content script, from background page. there are some data:' + data);
});
```

第二个参数为发送的信息，在注入脚本中通过request参数拿到
在contentscript.js中输入以下代码，开启对popup.js的信息发送监听，拿到信息request

```
chrome.extension.onMessage.addListener(function(request) {
  //request是popup page传来的信息
   console.log('get the message from popup.js'+request)
   //收到popup page的信息后要做的事情
});
```

注入脚本contentscript.js和浏览器打开的网页脚本web page运行在不同的环境之下，
所以他们的变量名和全局对象不会有冲突，
但是他们共享一个DOM树，也就是可以通过修改注入脚本的DOM结构来改变页面的DOM，
这也就是注入脚本同web page交互的方式

最后贴一个我练手写的一个[chrome插件](https://github.com/shudery/daguoNote)，
实现简单的便签功能，界面用amazeUI做的，
用React渲染，后台处理用Express搭建，数据结果直接保存一份JSON文件来管理
