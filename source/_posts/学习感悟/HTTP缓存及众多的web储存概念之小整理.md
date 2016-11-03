---
title: HTTP缓存及众多的web储存概念之小整理
date: 2016-08-12 01:46:57
tags: [HTTP]
description: this is a article for my way of studying front-end
cover: /images/2.jpg
categories: 学习感悟
---

缓存对于一个网站来说非常重要，可以提高网站性能，减少冗余的数据传输，增加服务器负担，web存储则给浏览器提供了更加强大的保存文件的接口。
有相当一段时间一直混淆了HTTP缓存相关的属性，HTML5离线存储和本地储存的一些关系，最近好好地整理了一下这些Web存储相关的东西

先列出一些相关属性和概念，看看能否理清它们之间的区别和联系？
<!--more-->

* manifest
* cache-control
* expires
* 304(no modified)
* ETag
* If-None-Match
* Last-Modified
* If-Modified-Since
* http-equiv
* webstorage
* cookie/session

是不是感觉有点凌乱，那就跟着我整理的笔记走一遍吧：）
首先说一说HTTP缓存相关的东西:
　　
　　
`Cache-Control`
每一个用HTTP请求的资源都可以在响应头用Cache-Control来给浏览器定义缓存策略，通过设置一些属性值它可以控制谁可以，在什么条件下可以缓存响应，还有缓存的有效期，这个属性的一些常用值如下

`no-cache：`表示不使用缓存，先和服务器确认要返回的资源是否有修改
`no-store：`表示禁止浏览器和所有中继缓存响应的资源
`max-age=100：`表示缓存的有效期，单位是秒，这一段时间内，除非缓存文件发生一些变动，否则会直接使用之前的缓存，注意这段时间内是不会发Etag等方法去验证的资源有没有修改的。缓存的文件发生变动，主要有这些情况：资源名更改，资源地址更改，缓存被删除，网页强制刷新等；资源名更改，给文件名添加版本hash值，比如给image.png修改为image-hash.png，可以保证每次更新文件时用户可以重新发出请求，获取最新的资源。资源路径更改，修改文件的请求路径，比如给image.png添加查询参数修改为image.png?hash，max-age的时间设置根据每个网站的实际情况不同去设置，一种极端的做法是把这个值设置很大，然后通过修改资源名或者给资源请求地址添加查询参数，来告诉浏览器该更新资源了，一般用在很久才更新网站的情况
`public：`用max-age即是默认public了，不用设置
`private：`私人缓存，中继缓存不被允许，但是可以在浏览器缓存

`PS：expires：`表示存在的时间，使客户端在这个设置的时间之前不用去请求资源，类似于max-age，但是expires表示的是一个固定时间，而且可能有服务器和客户端时间不一致的问题，主要用于HTTP1.0版本，在HTTP1.1版本完全可以用功能更强的Cache-Control来替代，和max-age同时存在时expiers会被覆盖掉
`PS：http-equiv：`缓存有两种控制机制，一种是请求头信息控制，另外一种就是利用meta标签；可以在HTML文档中为meta标签设置http-equiv为相应属性名，content为值来设置缓存，例如
`<meta http-equiv="Expires" content="Mon, 20 Jul 2009 23:00:00 GMT" />`，不过只对改网页的HTML文件有缓存作用，对该页面的其他资源以及其他页面的HTML文件都没有作用
　　
　　
那么max-age（expires）到期之后，在no-cache下的资源会先和服务器确认返回的资源是否有修改，如何实现这一过程？
`ETag/If-None-Match，Last-Modified/If-Modified-Since`
ETag (Entity Tag)其实就是一个验证令牌，用来标识一个资源，可能是一个hash值，也可能是一个版本号，每当资源有修改的时候ETag的值就会改变
浏览器第一次请求之后会保存响应头的ETag值，以便下一次发送请求的时候校验Etag是否有更改。
　　
　　
那么下一次浏览器如何告诉服务器本地已经存有Etag和相应的资源了呢？If-None-Match
通过在请求头添加If-None-Match(如果存在ETag，浏览器会自动添加)，赋值为上一次请求后在本地存储的Etag值，服务器会和服务端最新资源的Etag比对，如果没有更改会直接返回304 no modified给浏览器，浏览器就直接使用本地缓存的文件

`PS：Last-Modified/If-Modified-Since`的作用等同于`ETag/If-None-Match，`不过前者是通过规定一个时间来比对，最小的单位是秒，后者通过一个唯一标识符，所以可以看出来如果原站在一秒内有多次更新，那么前者就不顶用啦。
ETag的验证要优先于Last-Modified，此外ETag也是有缺点的，在分布式的环境中，Etag在不同服务器上的同步问题可能会给服务器带来一些压力。
　　
　　
`HTTP缓存`是和每一个HTTP请求直接相关的，每一个请求资源的响应都有相应的缓存策略，它们往往是相似的，是否可以通过其他的机制，直接告诉浏览器去缓存哪一些文件呢？
`HTML5离线存储`闪亮出场

H5离线存储：服务器通过一份.manifest文件给浏览器提供一份完备的缓存名单，名单包括需要缓存的文件，不需要缓存的文件的列表之外，还有一些其他的功能，比如给资源设置备选的请求地址，设置404页面等

实现：利用H5的标签新属性manifest，只需要在HTML文件添加`<html manifest="test.manifest">，`服务器则将manifest文件的mime-type设置为text/cache-manifest类型即可，浏览器每次请求会检查`manifest`是否有更新，服务端通过修改manifest文件，即可在浏览器下一次请求资源的时候通知其更新相应的资源。
作用：通过本地离线存储可以在没有网络的情况下访问网站事先保存的文件资源，在有网络的情况下直接使用本地资源也可以减少请求连接的压力，提高网页的加载速度，注意这里和HTTP缓存的区别，它是直接使用本地资源，请求返回的是200（from cache）,由于有manifest来统一管理，所以不需要发请求查看是否有更新，也没有过期时间。
　
`PS：`这里关于manifest文件自身的更新问题，还是要走HTTP缓存，或者直接不缓存这个文件。
　　
　　
前面提到的概念主要都是缓存请求文件这一块的东西，它们的目的都是为了提高网页的性能，可以说是一种优化型的存储，可以给用户带来更流畅的体验
但是我们印象中还有一种存储，它可以给我们提供更多的可能和效果的实现，是功能型的存储，如H5本地储存`Webstorage，cookie，session`
　　
　　
`cookie，session`
众所周知，HTTP是无状态的协议，每一次请求都是独立没有联系的，浏览器和服务器都没有办法维持用户的状态，判断用户是不是依然是之前的那个用户。
很容易可以想到，在同一个用户的每个请求头添加一个唯一标识符，通过判断这个标识符就可以维持用户的一些信息和状态
会话信息被用来作为标识符解决这个问题，cookie机制采用的是在客户端保持状态的方案，而session机制采用的是在服务器端保持状态的方案（服务端保持状态也需要客户端保持状态，所以一般session都要基础cookie或者sessionstorage）

总的来说：cookie数据放在客户端，session数据放在服务端，cookie可以设置期限，session则是关闭浏览器时销毁（cookie默认也是），cookie不安全，session可能会影响服务器性能

`Cookie：`通常用Javascript封装一个setCookie的函数来创建，有大小限制，可能会导致请求头过于臃肿，浏览器发送请求时，检查本地cookie，如果该cookie声明的范围大于发送请求的url地址时，就会自动在请求上添加cookie字段

`session：`服务器每次会检查浏览器请求头的session标识，如果有则将这个session id在服务器的数据库（散列表）查找，找到后才进行相应权限的操作
如果没有这个请求标识，则为客户端新建一个，返回给客户端后，客户端可以通过cookie或者sessionstorage来保存这个session id，通过请求头的cookie字段来给服务器提供sessionId，当cookie被禁止时就需要一些其他方法，通常是采用添加到url路径中，添加表单隐藏域等方法

cookie作为HTTP协议规范的一部分，主要还是用来储存用户信息的，用来和服务端交互，大小也有限制，仅仅只是一种会话级别的存储
　　
　　
`localstorage，sessionstorage`
`webstorage`为了更大的存储文件设计，与Cookie负责记录用户信息相比，webStorage专注于本地存储，通过封装好的setItem，getItem等方法即可使用
localstorage是一种持久化的存储，除非主动删除，否则永远存在，sessionstorage主要用来存储会话（session），关闭浏览器就会销毁，是一种非持久化的存储
　　
　　
除了上面这些的概念之外，还有诸如像IndexDB，FileSystem等存储方法，关于Web存储的知识真的非常多，看上去坑也是不少，还需要实践慢慢来掌握。









