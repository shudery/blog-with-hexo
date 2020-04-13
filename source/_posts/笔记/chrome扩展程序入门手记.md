---
title: chrome扩展程序入门手记
date: 2016-06-14 23:08:15
tags: [工具]
categories: 笔记
---

chrome 是我非常喜欢的浏览器，它除了速度飞快，对前端代码支持友好的优点外，
还有非常丰富的扩展程序资源，提供了大量方便强大的 web 页面应用插件
这两天由于朋友的业务需求，接触了一些 chrome 扩展程序(即俗称插件)的有关代码
基于对谷歌开发者文档的学习，随手记录了写 chrome 插件的基本方法~

每一个基本的 Chrome 插件,首先都有一个 manifest.json
的配置文件用来存储插件的基本信息

`"manifest_version": 2, "name": "Getting started example", "description": "This extension shows a Google Image search result for the current page", "version": "1.0"`

包括 manifest 的版本，扩展插件的名字，描述，版本

弹窗页面的信息 browser_action, 在里面配置一个插件弹窗 html 页面代码，一般名字是 popup.html，
浏览器界面显示的扩展图标位置，以及鼠标 hover 的 title

`"browser_action": { "default_icon": "images/icon.png", "default_popup": "popup.html", "default_title": "Click here!" }`

可选的页面注入代码，一般名字是 contentscript.js，
可选多个文件，匹配页面的 matches，只对匹配到的注入代码

`"content_scripts": [{ "js": ["jquery.min.js", "contentscript.js"], "matches": ["http://*/*","https://*/*"] }]`

还有插件要求的权限和要使用该插件的网页的匹配

`"permissions": [ "tabs", "http://*/" ]`

关于 manifest 的更多字段配置信息，可以查看 360 翻译谷歌的[开发者文档](http://open.chrome.360.cn/extension_dev/manifest.html)

从上面的配置可以看到，一个简单插件除了有 manifest 之外
还有一个 browser_action, 即 popup.html 来渲染点击插件之后的弹窗页面，
这个页面可以分离出来 popup.js popup.css 等文件，
这些文件可以放在同一个根目录下，文件中的引用跟普通项目一致可以取相对路径

如果要实现与浏览器打开页面的交互就必须还有一个注入脚本文件，
一般取名 contentscript.js，弹窗页面 popup page 通过相应 api 实现和注入脚本的交互
再由注入脚本实现对 web page 的交互
在 popup page（popup.js）中的输入代码

```
chrome.tabs.query({
      active: true
    }, function(tab) {
       var data = '';
        //要传递给注入脚本文件的信息
      chrome.tabs.sendMessage(tab[0].id, 'hello, content script, from background page. there are some data:' + data);
});
```

第二个参数为发送的信息，在注入脚本中通过 request 参数拿到
在 contentscript.js 中输入以下代码，开启对 popup.js 的信息发送监听，拿到信息 request

```
chrome.extension.onMessage.addListener(function(request) {
  //request是popup page传来的信息
   console.log('get the message from popup.js'+request)
   //收到popup page的信息后要做的事情
});
```

注入脚本 contentscript.js 和浏览器打开的网页脚本 web page 运行在不同的环境之下，
所以他们的变量名和全局对象不会有冲突，
但是他们共享一个 DOM 树，也就是可以通过修改注入脚本的 DOM 结构来改变页面的 DOM，
这也就是注入脚本同 web page 交互的方式

最后贴一个我练手写的一个[chrome 插件](https://github.com/shudery/daguoNote)，
实现简单的便签功能，界面用 amazeUI 做的，
用 React 渲染，后台处理用 Express 搭建，数据结果直接保存一份 JSON 文件来管理
