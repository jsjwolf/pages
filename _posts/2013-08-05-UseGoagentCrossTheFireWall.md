---
layout: post
title: 网络安全：局域网安全-监听和反监听
category: 安全
tags: [web安全,网络监听,反监控]
sharing: true
comment: true
robot: index,follow
alias: 
published: true
highlight: yes
description: "测试描述，看看不好好的的的，我们都是中国人额看不好好的的的，我们都是中国人额看不好好的的的，我们都是中国人额看不好好的的的，我们都是中国人额看不好好的的的，我们都是中国人额看不好好的的的，我们都是中国人额不好好的的的，我们都是中国人额"
---
## 准备说明
准备该片文章一段时间了，作为新博客上第一篇文章，甚是费心，待各种小伎俩融汇贯通，才敢来卖弄。如果熟悉TCP/IP协议族，则阅读本文比较轻松，如不然则也无大恙，毕竟本文只是一篇比较浅薄的入门介绍文档。

	$ lsb_release -a
	Distributor ID:	Ubuntu
	Description:	Ubuntu 12.10
	Release:	12.10
	Codename:	quantal


## 局域网为何如此不堪
网络数据安全或许可以分为两层：

局域网简称LAN（Local Area Network），对应OSI七层模型的三层以下（一般不含第三层IP层)，两层交换机或者共享式集线器组网，该类设备都不具有路由功能，故局域网内各节点间通过MAC地址（网卡物理地址）标识和通信，而MAC地址是由各个节点自行维护，这可能是局域网脆弱的根本因素。

局域网通信模型图示![LANArchModel][]

* 局域网链路安全

链路即网络数据包经过路线，如果节点A的数据包经过了节点B，则认为是不安全情况，因为节点B可以对经过的数据包作抓包和分析，有泄露隐私的可能。交换型方式组网的局域网默认情况下是链路安全的，因为每个节点之间都是单独链路通信；共享式组网的局域网默认下就是链路不安全，因为该情况下数据包都通过广播方式发送，同网段内仍一节点都可以收到包（需调整网络工作模式为混杂模式）。

* 局域网数据安全

数据安全理解为数据包如果被别人获取，包含内容是否可被查看到，没有加密而明文传输的数据包都是非安全的。如SSl/STL是安全的，而HTTP数据包一般是非安全的。数据包与实现协议有关，与网络拓扑结构无关。

大概所有局域网的攻击模式都是这样的，先攻击安全链路，如通过ARP欺骗或者DNS欺骗方式改变正常数据包传输链路，以获得数据包，抓取数据包则通过协议分析和多种手段取得数据包内容，最终达成攻击目的。


### 两个地址解析协议
#### 地址解析协议(ARP)
ARP(Address Resolution Protocol)，地址解析协议，当内网节点间（或者是节点与网关间）需要通信时，ARP解析获得目标IP地址对应的MAC地址，通过该MAC地址完成数据包发送。

#### 域名解析系统(DNS)
待补充...

### WLAN
待补充...



## 局域网攻击常用模式

###1. 抓包利器-Wireshark
[Wireshark][]（前称Ethereal）是一个网络封包分析软件。网络封包分析软件的功能是撷取网络封包，并尽可能显示出最为详细的网络封包资料。Windows下通过[Download](http://www.wireshark.org/download.html)页面下载安装即可，一般主流Linux发行版官方软件仓库已经集成该软件，可直接安装：

* Fedora

	$sudo yum install wireshark 
* Ubuntu

	$sudo install wireshark

默认wireshark运行需要root权限，忽略警告信息，选择对应的网络节点开始抓包，wireshark有两种包过滤机制：
	抓取时过滤规则，不符合规则的包直接PASS；详细规则使用参考[CaptureFilters][];
	显示时过滤规则，所有通过指定节点的包都抓取，仅显示符合规则的包在界面上，详细规则使用参考[DisplayFilters][]。

	Fiter:http or (http and ip.dst==204.232.175.78 ) or ( http and  ip.src==204.232.175.78)

###演习一次典型攻击
* ARP欺骗
* DNS欺骗	

### [Nmap][]扫描
参考手册[NmapManual][]

	nmap -sP  192.168.2.0/24
	nmap -sP  192.168.2.0/24

* 中间人攻击

## 局域网安全保卫战
* VPN跳出封锁
* Goagent定向保护
* HTTPS注意图标



通过Goagent+GAE技术突破内网http监听

## 准备环境
OS: Ubuntu 12.10 (X86)

###1. GAE
[GAE][]，全称Google App Engine，国内的SAE（Sina App Engine)是它的学生。Google应用云计算平台，支持Python/Java语言开发，意味着用这些语言实现的代码可以放到GAE平台跑起来(部分代码需要修改)，GAE根据应用的使用量计算费用，一定限额内可免费（量其实不小，具体参见[WhatIsGAE][])，需要有Google帐号并开通GAE服务（Google新用户注意）。GAE是本文所述方案实现的关键，正是这个免费的可运行代码的并且在局域网外（甚至是国外）的计算资源的存在，为我们跳出封锁提供了可能，下面就是如何利用这个资源实现设定方案。

###2. Goagent+SwitchySharp
[Goagent][]，官方介绍：A gae proxy forked from gappproxy/wallproxy.
Goagent分为两部分，一它实现了一个标准的http代理服务器，并且可以部署到GAE运行。二它同时可作为本地的http代理程序使用，二者配合工作。Goagent支持Windows/Linux/MAC/IOS/Android/...

[SwitchySharp][]，Chrome Proxy Setting Extension，一款有关Chrome浏览器代理设置的扩展，支持通过规则文件动态调整Chrome浏览器Http代理设置，这一点很有用，比如我们很关心在浏览taobao.com这条记录时不能被老板监控到，比如我们想要访问国外的facebook.com，此类站点设为通过Goagent访问合适，而当我们访问baidu或者内网的wiki页面时，则设为按正常访问合适，通过添加对应的匹配规则使得这些事情简单化。

Goagent+SwitchySharp部署过程分为下面几个步骤,以Windows为例（摘自Goagent官方页面）：

* 申请Google Appengine并创建appid，点击[GAE][];
* 下载Goagent最新版,点击[Goagent][];
* 修改local\proxy.ini中的gae字段下的appid="你的appid"(多appid请用|隔开);
* 上传GAE，双击server\uploader.bat(Mac/Linux上传方法请见[FAQ][]);
* 运行Goagent本地客户端（Windows运行goagent.exe，Linux运行proxy.py);
* Chrome请安装[SwitchySharp2][]插件，安装成功后在chrome地址栏右侧会出现一个小地球图标，推荐导入这个设置https://goagent.googlecode.com/files/SwitchyOptions.bak(该文件可在local目录下找到）；
* Firefox请安装FoxyProxy，Firefox需要导入证书，方法请见[FAQ][].

有关详细安装过程请参考[InstallGuide][]，详细Goagent配置文件说明参考[ConfigIntroduce][]。


## 原理介绍
	104         margin-left: 3em;
	105         list-style: disc;
	106         font-size: 95%;
	107         line-height: 170%;


	$sudo yum install wireshark 

## 相关参考阅读
如何对付公司的监控[1]：规避"网络行为审计" 
http://program-think.blogspot.com/2013/05/howto-anti-it-audit-1.html


[Wireshark]: http://www.wireshark.org/
[FilteringWhilegapturing]: http://www.wireshark.org/docs/wsug_html_chunked/ChCapCaptureFilterSection.html
[CaptureFilters]: http://wiki.wireshark.org/CaptureFilters
[DisplayFilters]: http://wiki.wireshark.org/DisplayFilters

[GAE]: https://developers.google.com/appengine/?hl=zh-cn
[WhatIsGAE]: https://developers.google.com/appengine/docs/whatisgoogleappengine?hl=zh-cn
[Goagent]: http://code.google.com/p/goagent/
[InstallGuide]: https://code.google.com/p/goagent/wiki/InstallGuide
[FAQ]: https://code.google.com/p/goagent/wiki/FAQ
[ConfigIntroduce]: https://code.google.com/p/goagent/wiki/ConfigIntroduce
[SwitchySharp]: https://code.google.com/p/switchysharp/
[SwitchySharp2]: https://chrome.google.com/webstore/detail/proxy-switchysharp/dpplabbmogkhghncfbfdeeokoefdjegm?hl=zh-CN
[Nmap]: http://nmap.org/man/zh/
[NmapManual]: http://nmap.org/man/zh/ 
