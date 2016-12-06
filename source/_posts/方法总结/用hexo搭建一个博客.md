---
title: 用hexo搭建一个博客
date: 2016-05-05 12:00:33
tags: [记录,工具]
description: 如何用hexo来搭建一个博客
categories: 方法总结
---
hexo出自一位台湾大学生之手，是如今搭建，管理博客，发布文章的非常好用的工具，
之前一直想搭建一个不依赖后台，便于管理和发布的博客，在朋友推荐下用了hexo
确实是简单粗暴好用~
基于hexo的博客搭建，对于已经配备了Node环境和git的前端开发人士，
搭建出一个博客那就是分分钟的事情，即使还没有弄好这些也不难搭建，
下面就从头大致说一说搭建的流程。
<!--more-->
### 创建一个github账号和博客
hexo搭建的博客是一个静态页面，可以直接托放到github上面的服务器上，
不需要拥有自己的服务器，所以我们要先有一个github账号，
然后需要生成一个github的博客，之后我们用自己定制的hexo博客来替换它，
如何搭建github技术博客可以看看这篇:[创建GitHub技术博客全攻略](http://blog.csdn.net/renfufei/article/details/37725057/)
### 搭建Node环境
我们还需要一个运行hexo和调试的环境，就是用Nodejs，
在[官网地址](https://nodejs.org/en/)选一个稳定版或者开发版一键安装好环境。
### 安装Hexo，生成文章
准备好nodejs之后打开命令行使用Node的包管理工具npm,直接在命令行运行
``
npm install -g hexo
``

下载hexo，然后再在相应的目录运行
``
hexo init
``

即可一键生成文件结构，你可以看到在source/_posts里面有一篇默认的helloworld.md文章，
在themes里面有一个默认的主题landscape，再继续运行
``
hexo generate
``

### 调试，部署到git
上面过程之后即可生成一个Public文件，里面会有你的文章内容，然后就可以运行
``
hexo server
``

启动localhost:4000，在浏览器输入地址就可以看到你的博客效果了，
如果你对默认的配置都满意的话下一步就是运行
``
hexo deploy
``

将内容部署到github上面去了，但是我们好像还没有建立hexo和我们账号github仓库的链接，
所以要在_config.yml最下面进行一些配置
```
deploy:
  type: git
  repo: git@github.com:shudery/shudery.github.io.git # 换成你的博客仓库地址
  branch: master
```
然后再hexo deploy即可完成部署，注意用ssh地址可以免去输入密码的繁琐，
当然前提是要先生成ssh密钥，并且在你的github上面添加这个ssh密钥~
hexo博客就已经搭建完成，可以在shudery.github.io上面看到博客页面和文章效果
### 添加文章，更换主题，修改配置
基本的东西就是这样，接下来我们可以在_posts里面以markdown的写作形式写文章，
然后通过同样的方式生成，部署，可以直接在_posts里新建md文件，
也可以用运行
``
hexo new [fileStyle] [fileName]
``

的方式来生成文件，后者会以scaffolds中对应的fileStyle的形式，
给你生成一个fileName的md文件，里面会包括一些开头的默认字段，
会方便记录一些文章的信息，我们还可以在[官网主题](https://hexo.io/themes/)里找一个更加符合心意的主题，
然后直接`git clone/ctrl+c` 到我们`hexo/themes`下面，
然后在`hexo/_config.yml`中的 `theme:landscape` 改为你下载好的主题名，
然后你会发现主题文件夹里还有一个`_config.yml` 用来修改主题的一些相关配置，
你可以参考你下载主题的github,或者官网的介绍来设置这些配置~

**由于无法忍受github服务器的延迟，目前已经将git远程仓库改为[coding.net](http://coding.net)了，这是一个国内的git仓库托管服务商，主要面向私密项目，和github一样同样有pages的博客搭建服务，只要在配置文件修改一下deploy地址即可**

### 完善博客
最后你可以根据自己的喜好和需求为博客添加一些你喜欢的挂件和工具，
我自己加了多说评论，百度统计，还有就是换了一个逼格高一点的域名，
通过添加CNAME域名解析，重定向到github.io的博客地址上面。

搭建过程中一些tips~

* hexo clean会清理缓存和Public文件夹一般是在hexo generate之前使用，但是不用没啥问题
* hexo/_config.yml对于hexo非常重要，但是如果去掉_config.yml你会发现还是依然能运行各种命令
* 然而一般运行时报错都是config配置有问题，而且很不好定位，
  所以可以注释掉config.yml里面你觉得应该不会导致报错的配置，然后再运行看结果，
  重复几次就可以定位到问题了，这对一开始没有什么经验的人来说是一个可行的debug方法。
* 在做域名重定向的时候，域名解析的CNAME需要指向github.io，
  此外还要在hexo/source下面也建立一个CNAME文件指向你的设置了重定向的域名地址才行