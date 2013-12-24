---
layout: post
subject: '利用BAE搭建Goagent代理服务突破内网封锁'
category: 'secure'
tags: '代理 翻墙'
keywords: '网络行为管理,Goagent PAAS,BAE搭建代理'
sharing: true
comment: true
alias: 
published: true
highlight: yes
postimg_s: /images/artist/skip_200x133.jpg
description: '前阵子微博上看到一句话：“他们不知道内网开发的痛苦”，深表同情。许多企业内部通过<b>上网行为管理系统</b>限制访问部分网站，比如像[Ubutnu中文论坛]也在某公司禁止之列，作为一名geek怎可忍受！'  
---

上网行为管理实现方法很多，但基本原理是在网关处增加包过滤机制，根据域名/IP、端口、协议等来确定是否允许访问通过，了解了这个突破封锁就好办，我们要：  

  * 隐藏真实访问地址；   
  * 改变连接端口和转换包协议（或者使用SSL加密）； 

理解了上面这段话，下来就简单了。我们需要搭一个代理服务器，所有被封锁的网站都通过代理访问，代理服务器与本地间使用加密方式通信，这样只要代理服务器可以访问，所有网站就都可以访问，实现这个目标具体三个步骤：
 
*1 寻找一个可托管php/python代码的服务器资源（BAE-百度云）；  
*2 配置代理程序(Goagent)：  
  ++ 上传代理程序的服务端代码，启用服务端程序；  
  ++ 配置代理程序的客户端，运行客户端程序；  
*3 配置浏览器代理，完成。  

##第一步：建立BAE应用
[BAE（Baidu App Engine）](http://developer.baidu.com/bae/),百度网络应用开发平台，当今云计算如火如荼，此属于[PaaS](http://zh.wikipedia.org/wiki/%E5%B9%B3%E5%8F%B0%E5%8D%B3%E6%9C%8D%E5%8A%A1)（Platform as a Service，全称平台即服务）一类，选择BAE因为亮点，一支持php/python，二每天提供200M免费流量，详细参考官方文档。

建立BAE应用流程：

*1 进入[注册页面](http://developer.baidu.com/bae)注册帐号；  
*2 收取激活邮件，点击激活链接激活并完善BAE开发者帐号信息；  
*3 建立BAE应用：通过 管理控制台 > 创建应用（假设名为goodProxy)，完成后保存；    
*4 添加部署：进入刚建立应用的管理主页，点击左边栏的“应用引擎”添加部署（类型选php-web，域名这里假设为goodproxy.duapp.com）；  
*5 记录该部署应用的仓库同步地址（git/svn)，后面要向该地址上传代理程序服务器端的代码。  

##第二步：配置Goagent代理
[Goagent](https://code.google.com/p/goagent/)，一款大名鼎鼎的上网代理软件。

Goagent由服务端和客户端两部分组成：  

+ 服务端有php和python两种实现，必须工作在被封锁的网络之外（这个事不能太细说，自己体会）；  
+ 客户端用python实现，支持Linux/MAC OS X/Windows系统，运行在本地计算机（又叫你的电脑）。  

goagent-v3.1.1-35目前最新发布的稳定版，[下载地址](https://nodeload.github.com/goagent/goagent/legacy.zip/3.0)(地址在墙外，请自行想办法)，该版本配置流程如下。

###1 解压程序包
解压goagent-goagent-v3.1.1-35-g0cb031b.zip，目录结构：
<pre><code>$ tree goagent-v3.1.1-35/ -L 1
goagent-v3.1.1-35/
├── local    
├── README.md
└── server
</code></pre>

###2 上传goagent服务端代码
本文推荐上传php版（即goagent-v3.1.1-35/server/php/index.php），上传地址从“建立BAE应用流程”第五步中获得；BAE支持两种方式上传代码：打包上传和git/SVN上传，根据情况选择上传方式。默认的代码有BUG，解决方法请查看本文最后的注意。 

###3 配置goagent本地端
修改配置文件"goagent-v3.1.1-35/local/proxy.ini"，使PHP一节的内容如下(**fetchserver的值请从“建立BAE应用流程”第四步中获得**)：  
<pre><code>
[php]
enable = 1
password = 123456
crlf = 0
validate = 0
listen = 127.0.0.1:8088
fetchserver = **http://xxxx.duapp.com/index.php**
usehosts = 1
</code></pre>

###4 运行goagent本地客户端  
Linux系统下运行方法：
<pre><code>sudo python goagent-v3.1.1-35/local/proxy.py    
</code></pre>
Windows直接双击运行：goagent-v3.1.1-35/local/goagent.exe  
成功运行类似如下输出：
<pre><code>
------------------------------------------------------
GoAgent Version    : v3.1.1 (python/2.7.3 pyopenssl/0.12)
Listen Address     : 127.0.0.1:8087
Debug INFO         : 1
GAE Mode           : https
GAE Profile        : google_hk
GAE APPID          : xxxxxxx
Pac Server         : http://127.0.0.1:8086/proxy.pac
Pac File           : file:///home/good/goagent-v3.0.8-61/local/proxy.pac
**PAAS Listen        : 127.0.0.1:8088**
**PAAS FetchServer   : http://xxxxx.duapp.com/index.php**
------------------------------------------------------
</code></pre>

##第三步：配置浏览器代理
代理地址为：127.0.0.1:8088，可以通过直接配置浏览器代理使用，也可通过浏览器插件来使用代理，插件可根据访问的地址智能判断是否需要代理。  

访问[站长之家] (http://ip.chinaz.com/)，如果地址显示北京，祝贺你！

Chrome浏览器推荐使用 [SwitchySharp](https://chrome.google.com/webstore/detail/dpplabbmogkhghncfbfdeeokoefdjegm)插件,然后导入 local/SwitchyOptions.bak 设置  
firefox浏览器推荐使用 FoxyProxy 插件  
IE/Opera及其他 用户请使用 IE 代理。


##补充几点
1 goagent本地与代理服务器之间连接使用弱加密，尤其是https，在涉及网银密码等信息时，谨防泄漏密码。    
2 默认的Goagent代理服务器端php代码有缺陷，要在最后加上一行：

	?>

##参考
详细参考：[https://code.google.com/p/goagent/](https://code.google.com/p/goagent/)  
FAQ：[https://code.google.com/p/goagent/wiki/FAQ](https://code.google.com/p/goagent/wiki/FAQ)  

全文完
2013-12-24


