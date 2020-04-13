---
title: 运用钩子：监听npm仓库和包更新
date: 2016-11-07
tags: [Node.js]
categories: 翻译
---

**本文由我翻译在[众成](http://www.zcfy.cc/claim)翻译平台，[文章地址](http://www.zcfy.cc/article/introducing-hooks-get-notifications-of-npm-registry-and-package-changes-as-they-happen-1610.html)**

今天我很高兴地给大家介绍一种简单强大的方法，可以用于追踪 npm 仓库变更，构建自己的开发环境的工具：钩子。

## What?

钩子是你订阅的 npm 仓库事件的通知者，运用钩子，你能构建可以响应 npm 仓库中软件包更改的处理各种任务的集成环境。

每一次软件包变化，钩子会发送一个 HTTP 的 POST 请求给一个你在钩子中配置的 URI 地址，你可以添加钩子来监听特别的软件包，监听包发布者的动态，或者监听一个组织里的所有软件包和用户。

举个栗子，你可以通过给**@npm**设置一个钩子，监视 npm 所有上传的软件包，如果你只想监视 lodash 这个包，同样可以给**@lodash**设置钩子。

## When?

如果你有一个 paid，[个人或者组织的 npm 账号](http://t.umblr.com/redirect?z=https%3A%2F%2Fwww.npmjs.com%2Fnpm%2Fprivate-packages&t=MGY4N2Q4OWRmYTljZDg2YzViMzQzZGY5YmE4ZWJiOGI3MDg2M2QwOSxycTRWMHI1eA%3D%3D)，那么你可以马上运用钩子。

每一个用户可以配置总共 100 个钩子，至于要如何使用它们，这就取决你了：你可以将它们都用于监听同一个软件包，或者监听 100 个不同的包。如果你用一个钩子监听某一个范围，那么算作一个单一的钩子，不论这个范围内有多少个软件包。你可以监视 npm 代码库中任何开源的软件包，以及任何你可以控制的私人包，你只能收到有权限访问的软件包的钩子通知。

## How?

![!full Slack integration](http://p0.qhimg.com/t01252b2385f2f7beb8.png)

现在用[wombat](http://t.umblr.com/redirect?z=https%3A%2F%2Fwww.npmjs.com%2Fpackages%2Fwombat&t=Y2JjNDUwNzc2ZWZiY2Q3NDMxZjVhM2ZmZTM2NGE3ZTU0Njc0MmEyMSxycTRWMHI1eA%3D%3D)命令行工具创建你的第一个钩子。

首先，用通用的方法下载 wombat

`npm install -g wombat`

然后开始设置一些钩子。

**1. 监听 npm 的软件包：**

`wombat hook add npm`

[https://example.com/webhooks](http://t.umblr.com/redirect?z=https%3A%2F%2Fexample.com%2Fwebhooks&t=ZDA5NzVhODQ0MjQ0ZmI2MzhmOGMzZjYwZTljZGEzYTBkMjIwZmU3YSxycTRWMHI1eA%3D%3D) shared-secret-text

**2. 监听@slack 组织客户 API 的更新：**

`wombat hook add @slack`

[https://example.com/webhooks](http://t.umblr.com/redirect?z=https%3A%2F%2Fexample.com%2Fwebhooks&t=ZDA5NzVhODQ0MjQ0ZmI2MzhmOGMzZjYwZTljZGEzYTBkMjIwZmU3YSxycTRWMHI1eA%3D%3D) but-sadly-not-very-secret

**3. 监听 ever-prolific substack:**

`wombat hook add --type=owner substack`

[https://example.com/webhooks](http://t.umblr.com/redirect?z=https%3A%2F%2Fexample.com%2Fwebhooks&t=ZDA5NzVhODQ0MjQ0ZmI2MzhmOGMzZjYwZTljZGEzYTBkMjIwZmU3YSxycTRWMHI1eA%3D%3D) this-secret-is-very-shared

**4. 查看你的所有钩子以及它们上一次触发的时间:**

`wombat hook ls`

提示：Wombat 还有几个有趣的用法。`wombat --help`会告诉你还有哪些用法。

我们也提供了公共的 API 来运行钩子，更多的细节以及如何运用 API 而不用 wombat 来管理你的钩子可以参考[文档](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fnpm%2Fregistry%2Ftree%2Fmaster%2Fdocs%2Fhooks&t=MDE2Y2IzMjJjMGVlOGVkMGY0ZDAwZDhhMzhjMTVmNjJjZGNiOWI4MCxycTRWMHI1eA%3D%3D).

## Why?

你能运用钩子来触发集成测试，触发代码部署，在聊天频道发布通知或者触发你自己软件包的更新

为了帮助你更好地上手，提供给你一些我们在开发钩子的时候整理的资料。

- [npm-hook-receiver](http://t.umblr.com/redirect?z=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fnpm-hook-receiver&t=OGRjZGI4YmEyYzQ0NDY0YzZjOTdiMzEzOGY5ZTE2MzM2ODViZThkOSxycTRWMHI1eA%3D%3D): 一个用钩子监听 HTTP 的 post 请求的[例子](http://t.umblr.com/redirect?z=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Frestify&t=OTI5YzEwOGFhNGE2NjhkYzg0ZWNjMzY5OTI5N2Y3YjM0YmNiZjc2YSxycTRWMHI1eA%3D%3D)，[源码点这里](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fnpm%2Fnpm-hook-receiver&t=Yjk3ZWI1OWE5YTg5ZmQ2NWM4Yzk4N2YwOGYwYjVlYTM1ZWE0NjZiNCxycTRWMHI1eA%3D%3D).

- [npm-hook-slack](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fnpm%2Fnpm-hook-slack&t=MGY1NTdjMjUwZjZkODJlZGI1YTFiNDJhNWI5YzNkZDcyOTc5NWI5YSxycTRWMHI1eA%3D%3D): 世界上最简单的播报 Slack 包事件的 Slackbot，用 npm-hook-receiver 构建.

- [captain-hook](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fnpm%2Fcaptain-hook&t=ZDc3ZDU2MDZjMjRlNjRkMGIyYmMyZDA2OTFkNjA3NGFhY2JmMmRiOSxycTRWMHI1eA%3D%3D): 一个有趣的工具帮你管理你的 web 钩子以及接收 posts.

- [wombat](http://t.umblr.com/redirect?z=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fwombat&t=NDBiZDBlZDM0OGY0OGYwOGFlNGU5YzdjYWJkNTUxNzgzMWZiOTU1OSxycTRWMHI1eA%3D%3D): 一个 CLI 命令行工具，用于检查和编辑你的钩子，这个客户端运用所有的钩子 API，[源码点这里](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fnpm%2Fwombat-cli&t=YzQxZTJiYWVkYTBhYmIyYmQzMTc4M2Y2ZTVmMjc3ZGMxOWRiNDY5MyxycTRWMHI1eA%3D%3D).

- [ifttt-hook-translator](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fnpm%2Fifttt-hook-translator&t=NjliNTU2YjkyZDVkNDEyOWMwZGFkODMxNGE5MzU5OWMzODVjMGQ4NSxycTRWMHI1eA%3D%3D): 一个可以接收 web 钩子已经将其转化为[IFTTT](http://t.umblr.com/redirect?z=https%3A%2F%2Fifttt.com%2F&t=ZWI1MzhmMzYxOGVlNTExNjYwYWY1ZWNlMWNhZDdiOTQ5ZmU5ZjBlMixycTRWMHI1eA%3D%3D)事件的工具, 可以用它触发任何你能在 IFTTT 做的事情

- [citgm-harness](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fbcoe%2Fcitgm-harness%2Fpulls&t=Mzk1MDA1MDUxNGE3NDAyMTljNzYxYTgyMDkxNWQ0Y2RhZTA4YmZhZixycTRWMHI1eA%3D%3D): [Canary in the Gold Mine suite](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fnodejs%2Fcitgm&t=ZTJkM2YzMzZmYzk5MWI3N2YyZTA0ZDRmZGE2N2ZmY2QyYmViMjJkMixycTRWMHI1eA%3D%3D) 一个 Nodejs 运用钩子来驱动包测试的工具，详细的包信息触发不同项目持续的集成测试，是一个测试是否破坏由顶自下依赖条件的方法

## What do you think?

我们公布的是钩子的 Beta 版本，从哪里获得它就行。我们如何看待它？你们还有其他想要的监听的事件？100 个钩子的提供太多还是太少？

我们对你的看法真的非常感兴趣，如果你构建了一些有用的或者用处不大的钩子工具，不要害羞与我们[交流](http://t.umblr.com/redirect?z=http%3A%2F%2Finfo%40npmjs.com&t=MGIzMWQ2MGMxZGVhODlkYTFkYmExNGFmZmYyNDQzNTBjZjg3YzQzZSxycTRWMHI1eA%3D%3D)或者在[推特](https://twitter.com/npmjs)上面@我们。
