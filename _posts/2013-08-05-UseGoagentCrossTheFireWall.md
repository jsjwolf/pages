---
layout: post
title: 通过Goagent+GAE技术突破内网http监听
category: 安全 
tags: [web, 安全]
sharing: true
comment: true
robot: index,follow
alias: 
published: true
highlight: yes
description: "测试描述，看不好好的的的，我们都是中国人额\
shib弟弟你的你的你恩呢你你你你你你的你你你\
ddddddddddddddddddddddddddddddddddddddddddddd"
---

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

###3. Wireshark
[Wireshark][]（前称Ethereal）是一个网络封包分析软件。网络封包分析软件的功能是撷取网络封包，并尽可能显示出最为详细的网络封包资料。Windows下通过[Download](http://www.wireshark.org/download.html)页面下载安装即可，一般主流Linux发行版官方软件仓库已经集成该软件，可直接安装：

* Fedora

	$sudo yum install wireshark 
* Ubuntu

	$sudo install wireshark


[Filtering while capturing][]

Fiter:http or (http and ip.dst==204.232.175.78 ) or ( http and  ip.src==204.232.175.78)

## 原理介绍

## 相关参考阅读

[Wireshark]: http://www.wireshark.org/
[FilteringWhileCapturing]: http://www.wireshark.org/docs/wsug_html_chunked/ChCapCaptureFilterSection.html
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
