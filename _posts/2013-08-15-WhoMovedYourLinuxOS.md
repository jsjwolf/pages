---
layout: post
title: '谁动了你的Linux系统'
category: 'secure'
tags: '安全 Linux'
keywords: '系统安全,系统体检'
sharing: true
comment: true
alias: 
published: true
highlight: yes
description: '通过定期体检可以发现身体潜藏的疾病，可能操作系统也一样，如果能定期对Linux系统做安全检查，可以使你的系统更加牢固。系统开放了那些端口(服务），是那个程序开的？是否遭受入侵或者攻击？我们下来看看。'
---

本文内容主要是多年使用Linux过程中，个人的一些积累，这里记录下来，与大家共同学习。文中以Ubuntu-12.10系统举例，但同样试用于所有Linux发行版。


## 常规检查

###系统版本信息

	$ lsb_release -a
	LSB Version:	core-2.0-ia32:core-2.0-noarch:core-3.core...
	Distributor ID:	Ubuntu
	Description:	Ubuntu 12.10
	Release:	12.10
	Codename:	quantal

系统安装软件包列表
	
	$ dpkg -l 

	$ rpm -qa 


###系统进程运行状态

	1 top - 15:34:24 up 23:44, 10 users,  load average: 0.52, 0.56, 0.59
	2 Tasks: 245 total,   2 running, 241 sleeping,   1 stopped,   1 zombie
	3 %Cpu(s): 14.3 us,  3.4 sy,  0.0 ni, 82.0 id,  0.0 wa,  0.0 hi,  0.3 si,  0.0 st
	4 KiB Mem:   3924136 total,  3220824 used,   703312 free,   296052 buffers
	5 KiB Swap:        0 total,        0 used,        0 free,  1488240 cached
	6
	7   PID USER      PR  NI  VIRT  RES  SHR S  %CPU %MEM    TIME+  COMMAND
	8 14846 good      20   0  733m 158m  43m S  33.8  4.1  71:54.21 firefox
	9  1309 root      20   0 98.6m  29m 6616 S  14.6  0.8  39:01.19 Xorg
	10 ...

说明：

- 第1行，与命令w输出的含义相同
- 第2行，进程总数245 
- 第3行，CPU使用情况，用户态（us）占用率14.3%，内核态占用率3.4(sy) 
- 第4,5行，内存使用情况，物理内存4G，空闲703M，交换分区大小为0 
- 第6,7,8行等，进程列表及实时运行状态 

###系统运行级和系统服务运行情况

	$ runlevel 
	N 2

待补充...



## 帐号安全

###系统用户登陆情况检查

	$ w
	 15:09:17 up 23:18,  4 users,  load average: 0.77, 0.59, 0.63
	USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
	demo     pts/2    192.168.1.100    15:12   1:16   0.30s  0.09s -bash
	gooo     tty7     :0               三15   23:18m 36:49   2.22s gnome-session --session=ubuntu
	gooo     pts/0    :0               三15   23:57   9.24s  9.01s /usr/bin/firefox

说明：
- 系统当前时间：15:09:17
- 系统已运行时间：23:18
- 系统平均负载：0.77, 0.59, 0.63 （over the last 1, 5 and 15 minutes）
- 已登陆用户：4个

	+ demo用户通过网络在图形下的terminal(pts指虚拟终端）登陆，登陆时间15:12，IP地址为192.168.1.100；
	+ gooo用户的第一个登陆在控制台七(即Ctrl+Alt+F5)，为当前gnome图形桌面的登陆用户；
	+ gooo用户的第二个登陆在图形下的terminal，正在运行firefox; 

*注意*：如果发现有不明用户登陆系统，可通过命令"pkill -kill -t TTYName"将其踢掉，如踢掉demo用户方命令如下：
	
	$ sudo pkill -kill -t pts/2

###系统用户帐号检查
检查系统中保存帐号列表的文件中是否有异常帐号。
	
	$ cat /etc/passwd
	root:x:0:0:root:/root:/bin/bash
	daemon:x:1:1:daemon:/usr/sbin:/bin/sh
	bin:x:2:2:bin:/bin:/bin/sh
	sys:x:3:3:sys:/dev:/bin/sh
	sync:x:4:65534:sync:/bin:/bin/sync
	...
	*test:x:0:1001:cs2c,,,,:/home/test:/bin/bash*
	

要特别注意类似test(最后一行）的帐号，这个帐号UID被设为0，意味这个用户权限与root完全相同，而且在Ubuntu下可以直接登陆（Ubuntu下默认是不允许root登陆系统的），非常危险。


###帐号登陆历史记录

last命令显示系统最近三次用户登陆情况，如果不指定-n参数，默认显示所有。

	$ sudo last -n 3

	用户名    终端         来源                登录时间     - 关闭时间（持续时间）
	gooo     pts/2        192.168.1.100    Thu Aug 15 15:58 - 16:07  (00:08)    
	gooo     tty5                          Thu Aug 15 15:12 - 16:09  (00:57)
	gooo     pts/7        :0               Thu Aug 15 15:06   still logged in
	

###帐号登陆失败录
lastb命令显示系统最近三次用户登陆失败的情况，如果不指定-n参数，默认显示所有。ubuntu系统下lastb默认不记录通过远程ssh登陆的失败记录，问题还在查找。

	$ sudo lastb -n 3

	用户名    终端         来源                登录时间     - 关闭时间（持续时间）
	UNKNOWN  tty6                          Thu Aug 15 16:04 - 16:04  (00:00)    
	root     tty6                          Thu Aug 15 16:04 - 16:04  (00:00)    

aureport是一个更加强大的系统审计信息查询工具，基于audit daemon，auditd根据配置规则记录相关系统活动，如用户口令修改记录、进程运行记录等信息，通过aureport工具可以查询audit日志。

	$ man aureport






## 网络安全

###服务端口开放检查

	$ sudo netstat -anpt

	1  Active Internet connections (servers and established)
	2  Proto Recv-Q Send-Q Local Address         Foreign Address       State       PID/Program name
	3  tcp        0      0 0.0.0.0:22            0.0.0.0:*             LISTEN      825/sshd        
	4  tcp        0      0 127.0.0.1:631         0.0.0.0:*             LISTEN      12489/cupsd     
	5  tcp        1      0 192.168.1.106:45268   91.189.92.11:443      CLOSE_WAIT  
	6  tcp        0      0 192.168.1.106:22      192.168.1.109:42066   ESTABLISHED 24652/sshd: 
	7  tcp        0      0 192.168.1.106:55095   192.168.1.108:22      ESTABLISHED 25221/ssh       
	8  tcp        0      0 192.168.1.106:32872   122.193.23.59:80      TIME_WAIT   -               
	9  tcp        0      0 192.168.1.106:46298   184.25.111.231:80     TIME_WAIT   -               
	10 udp        0      0 0.0.0.0:17500         0.0.0.0:*                         2579/drop
	11 tcp6       0      0 ::1:631               :::*                  LISTEN      12489/cupsd 

说明：

- 命令说明(A)：

	+ "-a" 显示所有；
	+ "-n" IP地址和端口号以数字方式显示，默认为别名方式；
	+ "-p" 显示PID和进程名；
	+ "-t" 显示TCP协议；
	+ "-u" 显示UDP协议。

- 第3行，*sshd服务(进程ID为825)开启了tcp协议的22号端口*，不限制接入IP地址；
- 第4行，cupsd服务开启了tcp协议的631端口，限制仅允许本地访问； 
- 第5行，本机正在访问91.189.92.11的443端口；
- 第6行，*远程主机192.168.1.109*已经通过ssh服务远程登陆本机（192.168.1.106)；
- 第7行，本机已经通过ssh服务远程登陆远程主机192.168.1.109；
- 第8,9行，本机正在访问122.193.23.59和184.25.111.231两个网站；
- 第10行，*未知服务（drop进程，PID2579)*开启了udp协议的17500端口，不限制接入IP地址； 
- 第11行，cupsd服务开启了tcp6协议(IPv6协议）的631端口，限制仅允许本地访问； 

###网络链路安全检查

默认网关检查

	$ route -n

网络设备节点情况

	$ ifconfig -a

实时网络连接状态和流量情况

	$ sudo iptstate -C

ARP解析列表缓存显示。如果192.168.1.1是网关IP，而后面的MAC地址与实际不符，那应该是被ARP欺骗了！

	$ arp -a

	?  (192.168.1.1) at 00:23:9e:xx:xx:xx [ether] on eth0


## 数据安全

如果有人入侵了你的系统，修改了系统启动脚本，还替换了一些关键程序来掩盖他的行踪，这该然后办？关键是你还不确定入侵是否发生过。
###RPM包完整性校验
Redhat系列发行版（Fedora/RHEL）提供RPM包数据校验功能，通过文件签名值校验安装到系统中的软件包是否已经被篡改，也可以扫描出非官方发布的软件包。校验系统中所有软件包情况：

	$ rpm -Va 

校验指定指定软件包initscripts：

	$ rpm -Va initscripts

	S.5......  c /etc/rc.d/rc.local

上例扫描结果：initscripts包中的文件/etc/rc.d/rc.local与初始版本内容不一致。

###DEB包校验
Ubuntu目前还没有找到对应功能。








