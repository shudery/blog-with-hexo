---
title: 简易的RSS格式输出工具
date: 2016-11-20
tags: [工具, Node.js]
categories: 笔记
---

RSS 即简易信息聚合，当一个网站发布一个 RSS 文件后，这个 RSS-Feed 中包含的信息就能直接被其他站点调用，而且由于这些数据都是标准的 XML 格式，所以也能在其他的终端和服务中使用，是一种描述和同步网站内容的格式。

由于我习惯用 mac 触摸手势的操作，以及不喜欢在浏览器里点击位于不同收藏夹里的标签，用 RSS 阅读器这种内容聚合方式，极大提升了我阅读文章，获取资源的效率，但是现在许多我喜欢的站点不开放 RSS 格式的输出源，或者已经停止维护老旧的 RSS 地址了，因此我将这些站点先进行爬取，提取相应内容，然后结合标准的 RSSXML 模板将其输出，使其能够被 RSS 阅读器正常的订阅。

下面我就介绍一下这个工具：[github 地址](https://github.com/shudery/RSS-feed-spider)

## RSS-feed-spider

#### 功能介绍

可以自己制作各大网站，内容频道的文章 RSS 格式输出源，通过爬虫工具抓取内容，输出 RSS 阅读器能够识别的 XML 格式，部署在 PASS 平台可以供阅读器订阅。目前由于很多站点二次爬取模板还没开发，故详情内容要跳转到原站，这对手势操作以及原本就想看原站的人没有大多影响，当然了，这意味着没法离线阅读内容，或者说事先缓存文章（只有标题）

#### 工作原理

利用 site 目录下存好的各站点基本信息，根据页面结构修改选择器，利用爬虫爬取页面，抓取相关信息填入 RSS-XML 模板中，将结果的 XML 文件返回到客户端，服务端使用 Node.js 和 web 应用快速搭建框架 express，爬虫用 superagent，pass 平台使用 heroku(国外不太稳定)

#### 查询参数

每个 RSS 订阅源的格式为：https://daguo-rss.herokuapp.com/?site={}
可以在订阅 url 后面添加查询参数，如?site=jianshu&num=20&desc=true

- site：站点名字
- num：抓取文章数量，默认 10，没有翻页逻辑，最多主页全部文章
- desc：是否抓取描述，需要二次爬取，不稳定，默认不开

#### 扩展方法

直接在项目 site 目录下生成相应的站点爬取脚本，脚本名字即对应的查询参数里 site={}需要填写的字符串。单次爬取的模板都一样，只需要配置相应的 RSS 基本信息，修改 getItems 函数中相应字段的选择器，这里用 cheerio 获取 DOM 节点。

#### 完成站点

- [简书热门频道](https://daguo-rss.herokuapp.com/?site=jianshu)
- [果壳](https://daguo-rss.herokuapp.com/?site=guoke)
- [掘金前端](https://daguo-rss.herokuapp.com/?site=juejin)
- [TED](https://daguo-rss.herokuapp.com/?site=ted)
- [前端早读课](https://daguo-rss.herokuapp.com/?site=zaoduke)
- [Hacker News](https://daguo-rss.herokuapp.com/?site=hacker)
- ~~电子科技大学 UESTC 新闻~~
- ~~下厨房流行菜谱~~
- ~~卢松松好文分享~~
- ~~微信自媒体~~
- ~~开发者头条~~
