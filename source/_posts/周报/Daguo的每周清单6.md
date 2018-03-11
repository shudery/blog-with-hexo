---
title: Daguo的每周清单：第六期
date: 2018-03-11 13:32:17
tags: [记录,技术]
description: develop cross-platform GUI app with electron
categories: 周报
---

回广东工作后，再次感受到了3月初就可以穿短袖的气候，不过才热了几天就又冷了回去，马上就是要迎接回南天的到来了吧？还记得前年去广州实习的时候连续3个多月的梅雨天气，广东的雨季，也是久违了。

<!--more-->

## 前端基础
- [谈谈 Vue 业务组件](https://zhuanlan.zhihu.com/p/33999571?utm_source=tuicool&utm_medium=referral)
> 来自饿了么前端团队对于组件和业务组件封装的理解，通常在写业务代码的时候，我们需要对一些基础的组件库进行封装，最简单的添加个样式，修改一下颜色，或者添加个图标，很多基础组件没有提供出来接口，有时候还需要写一些比较有入侵性的代码，比如根据组件内部的结构样式，写一些样式来改变展示效果，添加一些自定义方法等。

- [一劳永逸的搞定 flex 布局](https://juejin.im/post/58e3a5a0a0bb9f0069fc16bb)
> 现在写页面布局我都倾向于用flex来搞定，真的非常好用，在旧版浏览器已经被普罗大众抛弃的今天，它兼容性不足的问题似乎微不足道了。

## 技术视野

- [前端神器：一行命令，React 组件转 Vue 组件!](https://qianduan.group/posts/5a8e62160cf6b624d2239ca7)
> 很久之前就在想像element-ui和ant design这些前端组件库，基于某种框架实现后，为了迎合市场需求，必然会提供出多种框架实现，比如element原本是基于vue的，后来也退出了基于react和angular的版本，组件开发团队是如何重构的呢？基于一种框架实现，了解另一种框架语法后自己一个个去重构吧？后来我看到vue和react都支持render函数去渲染逐渐，这是一些语法差异，完全可能通过映射关系编译成不同的参数写法。然后我就看到了这个转换的工具，虽然不能完全无缝地编译转换React组件，但是可以节约很多重构组件的时间，如果在转换地过程能输出React哪些东西无法映射转换为Vue，并给出提示和方案就好了。

- [美团开源小程序开发框架mpvue](https://mp.weixin.qq.com/s?__biz=MjM5NjQ5MTI5OA==&mid=2651747630&idx=1&sn=dfb85acb20fbfcb4a8908917357be662&chksm=bd12ac638a652575eb5e542e8159b903b8061ceeb32434ae50e2631a039eddc01e01b2837b95&mpshare=1&scene=1&srcid=0308fhf1zmTBxZHHtSCHpRoN#rd)
> 这也是一个代码的转换工具，将vue工程代码转为小程序。感觉现在前端轮子越来越多之后，基于webpack的一些转换编译工具突然就火了起来，其实这是很正常的现象，原先我们开发小程序需要了解小程序的语法不说，小程序开发工程对组件化的不支持，让很多习惯了前端工程化的jser感觉很别扭。mpvue除了支持将vue语法直接转成小程序之外，使用前端统一的样式规范和标签集，让代码可以多端服用，不过现在还不支持vue-router这个比较重要的功能。原先的wepy其实也是解决相同的痛点，不过wepy只是基于vue的语法，但它并不是原生vue语法，还是有学习成本在。

- [看清楚真正的 Webpack 插件](https://zoumiaojiang.com/article/what-is-real-webpack-plugin/)
> webpack如今已经成为前端工程化事实上的基础，构建，打包，各种配置功能，如今已经很少看到曾经gulp和grunt的身影了，通过loader和plugin的不断丰富webpack的能力，像共同代码提取，markdown文档解析，服务代理等等，让前端开发越来越规范化。

- [我模拟了一个用浏览器挖矿的代码，没多复杂但别走歪路](https://mp.weixin.qq.com/s?__biz=MzIwMzg1ODcwMw==&mid=2247487547&idx=1&sn=ca3e0055978c4ad7d2deacc9503bdeaf&chksm=96c9a65ba1be2f4d5854e3d9041fac2b87541ec30f6c3cb9bd4c70701788994c94234ba88308&mpshare=1&scene=1&srcid=0309C6Wu3ECbEqDntnNYWe8o#rd)
> 区块链技术给予的启示，但我总觉得就是分布式计算的思想。

- [危险的 target="_blank" 与 “opener”](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651227961&idx=1&sn=d4eb72b910281a18fc35581e0e39096f&chksm=bd495ebd8a3ed7ab2dcc8d6bbfdd6f336f5b80a301cd3e7f92f56bdd3c95c749d9d6fd77282f&mpshare=1&scene=1&srcid=0310Ep0q8srjqfcy2KsmGrlY#rd)
> 前端的黑客技术总是给人惊喜，在非常简单的角落，用几个简单的API就能制造web攻击，之前完全没想到点外链跳转还有这种攻击操作，涨姿势了。

## 良心推荐
- [eolinker:国产的API管理工具](https://eolinker.com/)
> 在线API文档，支持代码注释直接抽取为API文档并在线部署，可以导入swagger文件，支持mock，自动化测试，减少前后端沟通成本。

- [The 105 Best Tools to Start Your Business in 2018](https://medium.com/the-mission/the-105-best-tools-to-start-your-business-in-2018-1675a457b4de)
> 创业团队必须知道的105款工具，几乎都来自美帝，不得不佩服这工具链实在是强大，亲测了几款都很好用。

- [飞冰 - 海量可复用物料，通过 GUI 工具极速构建中后台应用](https://github.com/alibaba/ice)
> 阿里开源的网页生成器，采用阿里淘宝内部最佳实践，统一工程目录和代码结构，解决淘宝的业务交接成本，目前支持的可复用物料，即组件是基于React的，阿里目前内部正在统一用React作为标准，但也像业界提供Vue相关的物料组件，后续支持。

## 总结&吐槽
感觉现在在技术上遇到一些瓶颈，想要突破 还是需要静下心来细读一些文章，实践一些代码呀。







