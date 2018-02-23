
---
title: 用hexo搭建一个博客
date: 2016-05-05 12:00:33
tags: [记录,工具]
description: 如何用hexo来搭建一个博客
categories: 方法总结
---

搭建一个不需要服务端的静态博客网站比较简单。
我推荐使用一个叫 [Hexo](https://hexo.io/) 的工具。
[Hexo](https://hexo.io/) 是一个快速、简洁且高效的博客框架。[Hexo](https://hexo.io/) 使用 [Markdown](https://daringfireball.net/projects/markdown/)（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。
> What is [Hexo](https://hexo.io/)?
> [Hexo](https://hexo.io/) is a fast, simple and powerful blog framework. It parses your posts with [Markdown](https://daringfireball.net/projects/markdown/) or other render engine and generates static files with the beautiful theme. All of these just take seconds.

通过下面几步就可以完成一个博客网站的搭建。

- 安装node环境
- 安装hexo
- 本机环境运行博客
- 安装git环境
- 注册github账号
- 使用github page服务
- 部署网站
- 进阶的网站配置

我们一步一步来。
### 安装nodejs环境
前往[nodejs官网](https://nodejs.org/en/)一键安装LTS稳定版本即可。
nodejs是javascript脚本的服务端运行时环境，因为Hexo框架是基于Nodejs的，所以需要预先安装。
安装后，打开你所使用系统的命令行工具输入`node -v`，通过是否有输出，验证安装是否成功。

### 安装hexo
在命令行中输入`npm install -g hexo`，安装成功后输入`hexo -v`可以看到下载的Hexo版本。
`npm`是nodejs的代码包管理工具。

### 本机环境运行博客
在命令行中输入`hexo init`将会在当前目录下新建一个文件夹，同时生成项目的初始目录结构，你可以看到在`source/_posts`目录里面有一篇markdown格式的文章，`source/_posts`就是你写的文章存放的目录，其他的目录可以暂时不用管。

hexo博客项目初始化后还需要在输入`npm install`来下载项目的依赖包。

`source/_posts`里的md文件就是你的文章内容，但是网站部署的html文件还没有，需要通过在命令行输入`hexo generate` 或者 `hexo g`来生成html和相应的静态文件，执行命令后会在根目录下生成一个Public文件夹，里面就是我们要部署到远端服务器的静态文件。

将博客部署到服务器之前，我们可以通过`hexo server`或者`hexo s`在本地运行博客，默认是在http://localhost:4000下运行，在浏览器中打开该地址就可以看到你搭建的博客的样子了，默认的博客主题是landscape，[Hexo主题](https://hexo.io/themes/)是可自由更改配置的，运行服务后你修改`source/_posts`目录下文章的内容，会实时地编译展示出来，方便调试。

接下来我们要把生成的Public里头的文件部署到服务器，一种做法是部署到你自己的云主机，但是公有云需要花钱而且国内还有备案问题，所以现在的静态博客比较流行托管到一些提供静态页面服务的网站，这些网站往往会给你提供一个二级域名，例如github提供的域名是https://username.github.io，如果你有自己的域名，也是可以更改的。然后你只需要把静态文件部署到github上即可，我们现在就通过这种方式来部署博客。

### 安装git环境
为了使用github的静态页面服务，需要使用git，和nodejs一样也是一键安装，到 [git官网](https://git-scm.com/downloads)安装对应系统版本。

### 注册github账号
到[github官网](https://github.com/)上注册个账号。
### 使用github page服务
有了github账号后，在github上新建一个仓库，会提示需要你验证注册邮箱，

验证成功后新建一个名字为username.github.io的仓库，

然后在仓库的setting中下拉找到GitHub Pages选择一个主题，

随便选择一个主题，反正后面我们会用Hexo生成的页面覆盖掉它。

然后可以看到系统已经给你创建一个https://username.github.io域名，可以成功访问。

### 部署网站
有了github page我们接下来要做的就是部署网站，将内容部署到github上面去，需要用文本编辑器打开根目录下的_config.yml进行一些配置。在文件内容底部找到deploy的配置项，修改为：
```
deploy:
  type: git
  repo: git@github.com:shudery/shudery.github.io.git # 换成你的博客仓库地址
  branch: master
```

然后就可以通过在命令行输入`hexo deploy`部署网站到github page了。

### 进阶的网站配置
到了这里一个可以发布文章，根据主题构建的博客网站就已经搭建成功并且实现远端部署了，但是网站还有很多可以完善和提升体验的地方，我简单罗列一下，有兴趣的朋友可以单独搜索看看具体的实现方法。

- 更换hexo主题，自己修改主题布局和结构
- 给文章添加标签和分类
- 添加github的ssh密钥，部署不用输入密码
- 修改CNAME文件，改变网站域名
- 给网站添加留言和打赏功能
- 更换其他的page服务提高速度，如coding.net
- 添加统计服务，记录博客访问数据