---
layout: post
subject: '微信管家源代码分析和文档汇总-持续更新'
category: 'web'
keywords: '微信管家,微信营销,源代码,代码,weixinguanjia,yyuc框架,yyuc手册,分析,下载,安装'
sharing: true
comment: true
alias: 
published: true
highlight: yes
postimg_s: /images/artist/wxgj_200x133.png
description: '这套微信营销系统代码500M多，功能比较完整，包括PC端web站、后台代理/用户分级管理系统和用户微信公共号后台功能定制系统三部分，但整个代码感觉是很多地方拼凑而来，实现粗放，有不少地方需要完善，但相对于不劳而获，已经算是很不错的东西。'

---

该套代码在网络上的关键字：微信管家最新官方完整版2014.03.03+官方补丁，有不少淘宝店也在叫卖。**关注微信O2O的创业者、小个体户老板或是其他研究微信公共号营销的同学可以好好研究下。**Goodbai先生欢迎各位在文章后面留言交流。

##源代码获取、安装篇
本套包含了两套微信源码，一套就是wx目录下面的，另一套就是wx/admin2下面的，两者的关系是，wx目录下的为主体，但有个别模块（如微餐饮/支付管理/微商城等）没有实现，而是直接调用了admin2的实现，事实上admin2目录下本就是另一套的完整功能的代码。  

* 下载地址  
百度云盘下载：http://pan.baidu.com/s/1kTFjXA3 密码：9p4k  
360云盘下载：http://yunpan.cn/Q4zhRaTsuuepp  访问密码 c9c8

下面介绍LINUX系统下的安装。  

### 第一步 准备环境
1. Linux安装http服务器+php+mysql环境，建议选择centOS+apache+php+mysql方案，具体方法请自行google;  
1. 配置默认mysql数据库root用户密码，安装phpmyadmin管理工具;  
1. 开启http伪静态支持。  

### 第二步 安装配置

1.上传
首先把根目录下的 WX 及 YYUC上传至网站跟目录 

2.还原数据库
将目录内的weixin.sql数据库文件还原至服务器建议使用phpmyadmin导入站点架设

3.以上步骤完成后，开始绑定域名，
只需将域名绑定到 WX目录下的public_html目录即可（也可直接把http服务器的root设置到public_html）。

4.配置文件
打开wx目录下的 conf.php 文件  并修改配置
/**数据库地址~*/
public static $db_host = "localhost";  
/**数据库端口~*/
public static $db_port = "3306"; 不用修改
/**数据库名~*/
public static $db_dbname = "weixin";
/**数据库用户名~*/
public static $db_username = "root"; 填写你的数据库账号
/**数据库密码~*/
public static $db_password = "******"; 填写你的数据库密码

完成上述操作第一步安装完成，输入ip或者域名，应该就可以看到首页。

### 第三步 配置第二套源码
配置第二套源码（即wx/public_html/admin2)。  

* 配置数据库信息  
  打开wx\pub\admin2\Conf 下的 db.php，修改数据库用户名和密码信息，如下：

```
         DB_HOST    对应  localhost
         DB_NAME    对应 weixin
         DB_USER    对应  数据用户名
         DB_PWD    对应  数据库密码
```

到这里安装完成，如果人品不差，通过首页进去看到的各项功能应该大多数可以正常使用。

##代码结构分析及优化
本套代码近700多M，代码结构是什么样？有没有隐藏的后门呢？代码是否可以精简？下面简单梳理一下。  

源代码解开后根目录结构如下：
```
├── weixin.sql       #数据库文件
├── wx               #主程序目录
│   ├── conf.php     #数据库配置文件
│   ├── controller   #controler目录
│   ├── fun          #函数库
│   ├── i18n         #国际化
│   ├── log          #调试模式下的日志输出目录
│   ├── model        
│   ├── plugin     
│   ├── public_html  #web访问入口，也是网站根目录
│   ├── pubupload
│   ├── sys          #cache和预编译后的视图文件目录         
│   └── view         #视图文件
├── yyuc             #yyuc开发框架
│   ├── controller
│   ├── forders
│   ├── plugin
│   ├── sys
│   └── yyuc.php
└── 安装说明.txt
```

本套代码基于yyuc框架开发，采用MVC开发模式，wx/controller存放控制器文件，wx/view/存放视图文件，在非开发模式下wx/sys/compilations目录存放预编译后（参考最后附录的yyuc开发框架说明文档）的视图文件，知道这个整个代码的结构就清晰了，每一个页面的可视html代码去wx/view(非开发模式时为wx/sys/compilations）找，页面业务逻辑去wx/controller找。

wx/public_html/目录作为全站根目录，其内部结构介绍如下：
```
$ tree  -L 1 wx/public_html/
wx/public_html/
├── admin2       #另一套代码，被间接调用          
├── bind 
├── bst.html
├── cache
├── css
├── heka
├── index.php   #全站入口页面
├── js          
├── media
├── public_html
├── res
├── static       #PC web首页目录，有三套模板，对应wxgjcom/wxgjcn/wxxxxx
├── Swipe-master
├── @system
├── upload
├── uploadify
├── ups
├── wqc        #后台用户上传资源存放目录
├── wxapi.php
└── zwj
```

所有页面相关的资源文件（js/css/image等）都在该目录下，包括用户图片上传目录。

### 明显需要修改的

1. 许多地方写死域名（www.weixinguanjia.com/），需要批量替换;
1. 部分wap页面包含有站长站统计代码（cnzz.com），需要批量替换;

## 常见问题列表
 1. 问题：访问注册页面（也就是reg.html）时，提示找不到reg.html文件？   
 答：需要开启web服务器的伪静态支持。  
 1. 问题：后台管理入口在哪里？  
 答：后台地址：你的网址/houtai  默认帐户(admin  123456789)  
 1. 问题：管理员数据库表是哪个？  
 答：管理员账号密码请到数据库weixin里面查看数据库表user_agency   
 1. 无法上传文件（图片）？  
 答：文件夹权限不正确，解决：chmod o+w upload -R  chmod o+w ups/ -R (解决微首页图片上传不成功）  
 1. 如何更换默认首页？  
 答： 安装完成后默认首页是旧版，切换成新版请修改wx/controller/index.php  里面最后几行的网址为你的网址即可
 1. 部分页面有乱码如何解决？  
 答：可能是数据库导入编码有误，建议使用phpmyadmin工具导入，步骤为使用phpmyadmin新建weixin数据库，再调整数据库编码为utf8-general-ci（否则会出现中文乱码），再移动weixin.sql到/var/lib/phpMyAdmin/upload/后，通过phpmyadmin导入功能导入数据。


##参考资料
YYUC与PHP开发框架比较（wang_quan_li）：<http://blog.csdn.net/wang_quan_li/article/details/21049125>  
微信第三方服务平台源码分析——每个Action与模块的对应关系：<http://blog.csdn.net/wang_quan_li/article/details/20958001>  
YYUC文档资料：<http://pan.baidu.com/s/1jGqPWWY#dir/path=%2F%E5%BE%AE%E4%BF%A1%2F%E5%BE%AE%E4%BF%A1%E5%BC%80%E5%8F%91%E8%B5%84%E6%96%99>  
安装参考1：<http://www.y154.com/source-code/website-source/374.html>（publick_html >pub)  
