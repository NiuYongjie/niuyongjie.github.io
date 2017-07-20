---
title: 路由器外网映射记录
tags: [路由器,外网映射]
grammar_cjkRuby: true
---

[toc]

这篇文章主要介绍如何在外网访问自己家中的路由器,是以后外网访问家中设备中服务的基础.

## 环境介绍

网络拓扑结构如下图所示:

![网络拓扑结构图][1]

联通光猫通过光纤链接Internet,路由器WAN口通过网线连接光猫LAN1口.两天电脑通过有线和无线的方式连接路由器.

光猫的角色主要有:光电信号转换,PPPOE拨号;路由器起到连接其他终端设备的功能.如果要在路由器或电脑中部署服务,并能够让外网访问服务,那么外网必须能够访问到路由器.

想让外网访问路由器有很多种方式,比如通过花生壳,DDNS等实现.在目前的环境中,光猫功能较弱,只提供拨号功能和简单的路由功能.不符合即将搭建的环境.决定采用以下的形式来让路由器能够被外网访问.

## 主要思路

1.将光猫的功能从路由功能变更桥接功能
2.让路由器拨号上网.这样,路由器就能够直接获得公网IP

## 实施过程

### 记录所需要的信息

1.在浏览器中输入192.168.1.1,进入光猫管理界面.

![enter description here][2]

有些产品的进入方式不同,可以通过光猫背部的提示信息进入
2.进入状态界面中的网络侧信息页面和网络配置页面,收集一下信息:
光纤端口的MAC地址
PPPOE拨号账户和密码

### 配置光猫

进入网络配置页面,没有桥接的选项，只有 pppoe 这一条:

!d[enter description here][3]

在谷歌浏览器中,按住Ctrl+Shift+C,单击PPPOE会弹出以下内容:

![enter description here][4]

删掉disabled,就会出现桥接模式的配置项,如下图:

![enter description here][5]

选择桥接模式(Bridge),重启光猫

### 配置路由器

进入管理界面中的上网设置.

![enter description here][6]

选择拨号方式为:PPPOE
输入之前记录的用户名和密码

![enter description here][7]

在MAC网址克隆选项中,输入记录的MAC地址

![enter description here][8]

保存重启路由器

至此,设置完成.现在路由器WAN口获得的地址就是公网IP.


  [1]: https://www.github.com/niuyongjie/imageBed/raw/master/1500527318334.jpg
  [2]: https://www.github.com/niuyongjie/imageBed/raw/master/1500531415399.jpg
  [3]: https://www.github.com/niuyongjie/imageBed/raw/master/1500532308332.jpg
  [4]: https://www.github.com/niuyongjie/imageBed/raw/master/1500532152784.jpg
  [5]: https://www.github.com/niuyongjie/imageBed/raw/master/1500532637035.jpg
  [6]: https://www.github.com/niuyongjie/imageBed/raw/master/1500587667050.jpg
  [7]: https://www.github.com/niuyongjie/imageBed/raw/master/1500587736270.jpg
  [8]: https://www.github.com/niuyongjie/imageBed/raw/master/1500587810416.jpg