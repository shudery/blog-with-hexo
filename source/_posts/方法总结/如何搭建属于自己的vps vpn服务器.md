---
title: 搭建属于自己的vpn服务器
date: 2016-06-10 21:59:48
tags: [教程]
description: how to build vpn or vps by youself
categories: 方法总结
---
之前实习的时候想着有需要翻墙的时候用着公司的vpn就足够了
但是随着马上要去工作，还有对一些国外资源的需求（资源你懂的）
然后也前后被一些网上的vpn服务商坑过（各种掉线，不稳定）
决定自己租用一台国外的服务器vps
<!--more-->
了解一番后知道了国外[搬瓦工](http://bandwagonhost.com/)和[digital ocean](www.digitalocean.com/?refcode=3491087221da)都有不错的口碑，最终选择DO,
最便宜的512MB内存，20GB固态硬盘，1TB流量，

这个好像只是防止恶意使用的，实际上流量是用不完的，
在服务器控制版面也没有流量使用情况提示
首先注册账号，输入邮箱密码，在邮箱收到确认邮件，
来到Payment Methods 

![](https://raw.githubusercontent.com/shudery/public/master/Pictures/article/vpn_1.png)

这一项我选择的是方便快捷的PayPal，Credit不好设置
需要预充5刀PayPal，5刀也是最便宜的服务器的月租价格，
如果没有PayPal的话就先注册一次，比这个Credit方便多了
注意一些老旧的银联卡号可能提示设置错误，我换了一张卡就好了

然后开始选择服务器，包括价格，地点，系统，
我选了最便宜已经够用了的5刀/月，ping后较快的美国西部服务器，
以及比较容易操作的ubuntu系统，然后添加主机的SSH密钥，以后方便，

不过也可以先跳过以后再说，买后会收到邮件，
得到一个服务器主机Ip，root账户名，以及一个初始密码。
vps算是有了，接下来我们在这台服务器上搭建vpn

然后不得吐槽的是网页的console控制台实在有点卡卡，
所以我用简单的putty来建立与vps的链接，也可以使用xshell等工具，
下载putty后直接输入ip地址，保留默认端口，点击open就进入控制台了

然后输入你的用户名root，然后出现password输入密码，
这里注意啦~输入密码的时候，光标是不会动的！
所以，慢慢输入，不要输错了，登录成功后显示一些信息

![](https://raw.githubusercontent.com/shudery/public/master/Pictures/article/vpn_2.png)

第一次登陆要求重置密码，分别填一次旧密码和两次新密码，注意光标还是不会移动！
然后开始一列Linux操作，作为小白的我们就只管输入代码即可，
不过我们也是有(yao)追(zhuan)求(bi)的小白，所以也要大概知道这些代码是个什么作用

### 1、安装vpn服务
首先我们必须在vps服务器上安装一种vpn服务，这里选择点对点隧道协议pptp,输入

```
apt-get install pptpd
```

### 2、配置文件修改
用vi编辑器打开配置文件，输入

```
vi /etc/pptpd.conf
```

没用过vi的注意啦，要编辑文件必须先输入i，进入INSERT模式，
将光标移动到最下面更改

```
localip 10.0.0.1
remoteip 10.0.0.100-200
```

有两段，localip更改为你vps服务器的ip地址，remoteip是以后分配给
其他连到你vpn的服务器的ip，可以照着例子分配，写完后保存，
vi的文件保存方法是先按Esc然后输入冒号:wq即可，w是写入保存，q是退出vi

### 3、添加vpn账号
用vi打开密钥文件，输入

``` 
vi /etc/ppp/chap-secrets 
```

依次输入 username pptpd password ### 
将username, password更换为你的vpn账号和密码，中间是服务名，
最后一个是ip通配符，如果要建立多个vpn账号给妹子基友一起用，
还一行依次输入即可，输完同样保存

### 4、设置公共DNS服务，输入

```
vi /etc/ppp/pptpd-options
```

打开服务选项文件设置找到ms-dns并设置为
`
ms-dns 8.8.8.8
ms-dns 8.8.4.4
`

### 5、重启pptp服务，
重启服务，刷新配置，输入

```
service pptpd restart
```

### 6、ip转发配置
打开转发配置文件，输入

```
vi /etc/sysctl.conf
```

发现整个文件都带有注释符#,去掉`# net.ipv4.ip_forward = 1`
前面的注释符#保存,为了使配置生效还需运行

```
sysctl -p
```

### 7、设置iptables
设置并保存，运行

```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE && iptables-save
```

至此搭建好属于你自己的vpn，拿着账号在手机和电脑的vpn设置登录即可
注意如果出现vpn隧道协议构建失败，检查一下网络适配器里vpn的安全属性
勾选为以下选项

![](https://raw.githubusercontent.com/shudery/public/master/Pictures/article/vpn_3.jpg)







