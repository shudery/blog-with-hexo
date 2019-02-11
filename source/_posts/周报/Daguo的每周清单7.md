---
title: Daguo的每周清单：第七期
date: 2018-04-01 13:32:17
tags: [记录,技术]
description: develop cross-platform GUI app with electron
categories: 周报
---

这周朋友圈刷起了下雪的照片和视频，想起在成都四年还是没能一睹雪花纷飞的场景，很是遗憾啊。还以为广东会保持18度的秋天风格到过年，下午就收到了明天骤降10度的寒潮预警，怕冷的人瑟瑟发抖！
<!--more-->

## 前端基础
- [前后端分离实践](https://mp.weixin.qq.com/s/nKvjsU2frT5NDU4DLWqvYg?scene=25#wechat_redirect)
> 一直以为前后端分离是业界常态，接触的项目比较多了之后，发现很多项目还是保持着和后端的强耦合，接口上依赖后端实现，不事先设计和mock，部署上和后端JAVA代码打成包才能发布，不能独立发布等等情况还很多，很影响开发效率。除非项目就是一帮全栈工程师写的，否则我认为前后端分离应该积极的践行。

- [`(a==1&&a==2&&a==3)`有可能是 true 吗？](https://www.tuicool.com/wx/Z3uaMzA)
> Javascript确实神奇，很久以前看到过类似的toString的这种用法，但是再次接触到考这个知识点的一个变种题目还是觉得非常有趣。

## JavaScript生态
- [微信小程序和PWA对比分析](https://zhuanlan.zhihu.com/p/35151153?utm_source=wechat_session&utm_medium=social&from=timeline&isappinstalled=0）
> 小程序和PWA有着很多相似的地方，技术上都是通过Web技术实现可实时更新和跨平台，通过底层android/微信的支持接口提供平台能力，可以离线使用o，相比于传统的APP产品有比较低的实现成本和高的可移植性，如果能处理好性能体验的问题，PWA和微信小程序的生态都会得到迅速地发展。


## 技术视野
- [TensorFlow发布面向JavaScript开发者的机器学习框架TensorFlow.js](https://juejin.im/pin/5a695e3b51882563db3cb72d?utm_medium=pwapyq&utm_source=weixinqun&from=timeline)
> 随着谷歌的机器学习框架TensorFlow发布TensorFlow.js，感觉js也把生态魔抓伸向了一直由python占领的机器学习领域，非常兴奋，先mark后学

- [Bootstrap 4 发布了，可是已经过气了呀](https://mp.weixin.qq.com/s?__biz=MzIwNjQwMzUwMQ==&mid=2247485752&idx=1&sn=c59b754ad87382c9bb0f67466036a830&chksm=97236bfaa054e2ecf4f2c9b904c99f914b4124cd96bbef00638b2ebf083b0dacf85b9abcee4f&mpshare=1&scene=1&srcid=0128yJYuM72IEU8mokTjIfoo#rd)
> Bootstrap的第四号版本三年磨一剑，从15年开始Bootstrap4的第一个alpha到现在，说起来那会我还没入行呢，前端技术圈已经发生了翻天覆地的变化。想起自己当年也是从Bootstrap开始撸起页面的，如今由于更新迭代缓慢，同质化和紧耦合，缺少语义而被人诟病，曾经的王者陨落，时代的眼泪啊。

## 工程实践
- [用JavaScript写一个区块链](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651228053&idx=1&sn=38e580fcf3d059120c4498cdaa4a20a0&chksm=bd495e118a3ed707a0ef2320e748c81a673ec402bdd74969b653e45c3f8ee7d0ae5b6b13dd56&mpshare=1&scene=1&srcid=0316Sj7aMWiooaoXiEEmbmuv#rd)
> 通过用前端er熟悉的javascript写出来的区块链应用，实现了区块链的一些基础功能，学习理论后配上实践来理解总是更加容易。

- [本周开发手记](#工程实践)
> 大公司的技术产品，尤其是内部应用产品往往采用非常陈旧的技术栈实现，稳定可靠但是同时积累着一大堆技术债务，可维护性和代码结构越来越差，同时各个模块使用不同的前端技术，难以复用各种业务功能组件。考虑到不同的前端MVVM框架有自己的一套组件系统，如何才能建立一个统一的通用的组件库，其他存量的系统可以直接引用呢？比如我们统一建立一个Vue的组件库，封装了一些Vue的业务功能组件，那么其他的产品如果使用的是react或者angular要如何接入？一种方案是参考业界的将Vue组件库重构一个react版本和angular版本，但是由于需要采用这种策略需要重构的工作量和人力完全无法匹配，我们目前暂时采用了第二种，就是直接引入vue组件依赖的包括vue.js在内的所有文件，好在vue所有的依赖加起来也就100K，这毫无疑问会影响原先页面的性能，所以我们也在积极地推部门使用同一的前端技术栈，这样就可以无缝使用组件库的组件了。除了性能问题，兼容性问题也是需要考虑的点，虽然现在通过在index.html绑定不同的dom可以实现各自框架的逻辑没有问题，但多个框架总是让我觉得不太放心。

## 良心推荐
- [KeystoneJS](https://segmentfault.com/a/1190000004646121)
> 上周推荐了小程序的后端服务接口商城，除了小程序后端，我们经常在开发完页面之后需要做一个管理后台来可视化地操作我们的持久化数据，同时后端需要配置路由和数据库，所有的这一切Keystone都能帮助前端工程师解决，它基于express，让前端又一次“全栈”。

- [Vue2实战教程](https://item.jd.com/12176536.html)
> 来自梁睿坤大佬的实战经验书籍，Vue2实战教程可以说是干货满满，通过实际项目讲解Vue开发一个项目需要注意的方法面面，有很多概念解析很有想法，踩过的一些坑也是无私分享，本周看了三分之二，就忍不住强力推荐啦。

## 效率工具
- [CSS用法快速搜索](http://cssreference.io/)
> 在知乎上看到的，可以快速搜索CSS的一些属性，一般我通过模糊搜索找到自己有点忘了怎么用的一些属性，点进去后，神清气爽，所有的demo和注意事项都给你列出来了，瞬间就加深了对属性的了解，而且界面非常好看。

## 总结&吐槽
本周虽然每晚都是十点后回家，8106，好像是来部门后过得最忙的一周了吧，但是感觉也是最充实的一周，技术上做了很多技术方面的预研，学习了一些项目启动，工程化的方法论，通过试验写demo实践了许多存有疑惑的知识点。下周又有新的任务，要适应多核工作的局面，另外个位数的温度重回广东，还得适应下寒冷。下周想把我的大F带到公司去。

说起来好像接下来每周都要上6天班，知道年后回来的第二个星期，看来周末又出不了坂田这块地了。






