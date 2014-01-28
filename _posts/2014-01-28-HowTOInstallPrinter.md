---
layout: post
subject: 'linux桌面打印机配置指南'
category: 'linux'
keywords: 'cups,打印机适配'
sharing: true
comment: true
alias: 
published: true
highlight: yes
postimg_s: /images/artist/printer_200x133.jpg
description: 'Linux下打印机适配难，主要是Linux版本太多，导致厂商无力也无太大兴趣关心其，个人刚好接触过这一块，专门整理一份文档方便查阅。 '  
---

目前Linux系统主流使用[CUPS](http://www.cups.org/)（Common Unix Printing System)打印服务系统。

##HP printer
[HPLIP](http://hplipopensource.com/hplip-web/index.html)(HP Linux Imaging and Printing)开源项目提供的Linux驱动可以支持市面上大多数HP打印机，部分的打印机可能需要额外安装**插件**，插件一般为固件（.fw文件）或预编译的共享库（.so文件）（**需要插件的设备一般不支持国产龙芯和ARM平台之类**）。  

支持情况查询：http://hplipopensource.com/hplip-web/supported_devices/combined.html  
插件安装参考：http://hplipopensource.com/node/309  
经分析，插件下载地址是: http://www.openprinting.org/download/printdriver/auxfiles/HP/plugins/  
安装目录: /usr/share/hplip/scan/plugins/ 
 
## Canon printer
驱动地址：
http://www.canon-europe.com/Support/Consumer_Products/products/printers/Laser/Laser_Shot_LBP1120.aspx?DLtcmuri=tcm:13-1060371&page=1&type=download#

## 几个Linux重要的打印机驱动包

###HPLIP
[HPLIP](http://hplipopensource.com/hplip-web/index.html)上面已经介绍过，HPLIP开源项目由HP官方支持，包括打印和扫描扫描设备的驱动，同时包括hp-setup/hp-check等命令可供现场适配调试用。  

###foo2zjs
[foo2zjs](http://foo2zjs.rkkda.com/)，Linux著名的第三方开源打印机驱动，对于它的作者我一直没有搞清来历，只在项目主页看到一句充满愤怒的话，如下：
> *** DON NOT USE the foo2zjs package from:  
>     	 Ubuntu, SUSE, Mandrake/Manrivia, Debian, RedHat, Fedora, Gentoo, Xandros, EEE PC, Linpus, MacOSX, or BSD!  
> *** Download it here and follow the directions below.  

foo2zjs项目有几个入口，但内容都是一样的，内包含多个小模块（foo2zjs/foo2slx/foo2qpdl/foo2xqx/foo2hbpl2等），可以直接从发行版仓库安装，也可自行下载源码包安装（编译安装方法参考包内INSTALL文件或主页说明）。  

**注意：**
1 可能有多个驱动同时支持一款打印机；  
2 HPLIP项目中列出需要插件的HP打印机，如果使用foo2zjs驱动，同样需要安装插件。

### gutenprint
没有仔细研究，建议直接通过发行版安装。

##Linux系统打印机适配流程参考
鉴于Linux系统小众的原因，不是市面上每一款打印机都一定能适配成功，但可参考下面方法尝试。  

1. 尽量升级驱动到最新版
如升级CUPS/foo2zjs/hplip/guteprint/，或者是换最新的Ubuntu/Fedora发新版。  
1. 查看硬件设备是否已识别，一般OK，否则可能是设备损坏或者连接有问题。  
```
$ lsusb
```
1. 查看打印系统（CUPS）是否识别设备  
```
$ sudo /usr/lib/cups/backend/usb  #针对USB打印机
```
如果是HP打印机，可使用hp-setup命令以向导模式安装。

## 其他辅助操作
###lpadmin命令操作打印机配置
```
/usr/sbin/lpadmin -p TOEC--Printer -m OEP102B.ppd -v usb://TOEC/Printer  #添加  
/usr/sbin/lpadmin -d TOEC--Printer  #设为默认  
```

###打印机后端调试
```
/usr/lib/cups/backend/usb #发现usb打印设备  
CUPS_DEBUG_LEVEL=2 su-c " /usr/lib/cups/backend/ipp" 2>&1 | tee snmp.log #后端调试  
usblp.ko #默认匹配多数usb打印机，适配施乐p105b时发现，自动加载模块导致无法发现设备（移除该模块后正常）  
```

### 查看CUPS日志
```
/var/log/cups/error_log
```

### Web方式管理CUPS打印系统
CUPS默认开启web后台管理服务，浏览器访问<http://127.0.0.1:631/>即可。  

## 龙芯平台Canon Laser Shot LBP1120 适配记录
驱动地址：<http://www.canon-europe.com/Support/Consumer_Products/products/printers/Laser/Laser_Shot_LBP1120.aspx?DLtcmuri=tcm:13-1060371&page=1&type=download#>
###源码编译
README中说明了依赖、支持的设备及支持发行版情况。  
源代码分为两部分：cndrvdups-common和cndrvcups-capt。  
cndrvdups-common  
参考README，执行（已完成）：  
```
make gen
make 
make install [DESTDIR=./root]
```
cndrvcups-capt  
```
./allgen.sh
```
错误：../libtool: line 841: X–tag=CC: command not found ...  
解决：
缺少$echo定义导致（有$ECHO)，在allgen.sh添加export echo=echo(make install前也要添加).  

###安装调试
按上面的步骤成功编译和安装驱动后，接入打印机自动生成配置，打印测试页失败。比较发现该下载包中预编译的RPM包，要多出一些文件（如/usr/bin/ccpd等），对应到源码下libs目录也有一份预编译的x86的二进制文件，看来canon驱动只是部分开放，下面的说明也验证了这个说法。

http://forums.fedoraforum.org/showthread.php?t=280582  
http://www.unixmen.com/installation-canon-lbp2900-on-linux-2/  

具体说，canon的这个驱动在cups和设备之间加了一个黑盒层（不开放源代码）ccpd，通过该守护进程实现功能，而该ccpd守护程序当前无法在Mips下运行。

该情况发送邮件询问canon欧洲官方支持，结果告知问题要发给canon中国解决，如下：

>Dear good bai, 
>
>Thank you for having contacted Canon Europe customer support. 
>
>In order to provide you with the best response to your query, we have forwarded your >request to the local Canon Services & Support organisation responsible for your >geographical region. 
>
>Please do not hesitate to contact them directly at the following email address: >http://www.canon.com.cn/support/service.html
>
>Yours sincerely, 

而canon中国根本就不提供Linux驱动的下载，该驱动就是从canon欧洲官方网站下载到的。

初步结论：该驱动不支持龙芯平台（经官方方确认）。
###UFR版驱动
UFR II Printer Driver for Linux Version 2.60：http://support-cn.canon-asia.com/contents/CN/ZH/0100270809.html   
Information on Printers from Canon：https://www-304.ibm.com/support/docview.wss?uid=nas8N1019527  

URF驱动包与capt包类似，目前编译完成，可自动添加，开始报过滤期丢失，后来驱动无效。。。

**补充：canon中国客服告知，LBP2900的唯一驱动是capt（http://support-cn.canon-asia.com/contents/CN/ZH/0100459603.html，这个与canon欧洲的capt驱动完全一致）**

**结论：该Canon打印机驱动可以支持X86 Linux系统，但无法支持龙芯等国产平台（因为预编译库为x86版）**

##通过cups配置打印机共享
* Server端的设置  
 1 在“打印机设置”界面中，右键单击想要共享的打印机，在菜单中“共享”一项前打“√”，表示启用共享。  
 2 在“打印机设置”界面中，单击“服务器”，选择“设定”，输入root密码正确后，在弹出的“服务器设定”对话框中，勾选“发布连接到该系统的共享打印机”，其子项“允许从互联网打印”也要勾选。如果提示“更改防火墙”，选择更改防火墙。至此，server端设置完成。

* Client端的设置  
 1 单击“启动”，选择“系统”--->“硬件”--->“打印”，打开“打印机设置”界面。  
 2 “打印机设置”界面打开后，单击“添加”按钮，输入root密码，出现“新打印机”界面  
 3 在此界面中选择“查找网络打印机”，在右边“主机”中输入server的ip地址，然后点击“查找”。  
 4 可以看到已经查找到网络打印机，单击右下角“连接”按钮，出现“IPP”选项，选择“队列”名为：/printers/HP-LaserJet-M1522nf-MFP的“IPP”。

##扫描仪
关于HP扫描仪，通过与launchpad.net（hplip） 技术人员沟通知悉，hplip扫描的插件不支持mipsel架构，所以龙芯下不能扫描。  
详见：https://answers.launchpad.net/hplip/+question/198640



