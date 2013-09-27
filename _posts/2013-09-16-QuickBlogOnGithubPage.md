---
layout: post
subject: '使用Github Page搭建最快的博客'
category: 'web'
tags: 'github_page html jekyll'
keywords: 'github page'
sharing: true
comment: true
published: true
highlight: yes
postimg_b: /images/GibhubPage/github_600x400.png
postimg_s: /images/GibhubPage/github_200x133.png
description: '通过定期体检可以发现身体潜藏的疾病，可能操作系统也一样，如果能定期对Linux系统做安全检查，可以使你的系统更加牢固。系统开放了那些端口(服务），是那个程序开的？是否遭受入侵或者攻击？我们下来看看。'
---

如何使用Github Page搭建博客的文章，网上已经很多，图文并茂很是详细。本文重点介绍如何搭建*最快*的博客系统

## 常规检查

说明：

- 第1行，与命令w输出的含义相同
- 第2行，进程总数245 
- 第3行，CPU使用情况，用户态（us）占用率14.3%，内核态占用率3.4(sy) 
- 第4,5行，内存使用情况，物理内存4G，空闲703M，交换分区大小为0 
- 第6,7,8行等，进程列表及实时运行状态 


	daemon:x:1:1:daemon:/usr/sbin:/bin/sh
	bin:x:2:2:bin:/bin:/bin/sh
	sys:x:3:3:sys:/dev:/bin/sh
	sync:x:4:65534:sync:/bin:/bin/sync
	...
	*test:x:0:1001:cs2c,,,,:/home/test:/bin/bash*
	

要特别注意类似test(最后一行）的帐号，这个帐号UID被设为0，意味这个用户权限与root完全相同，而且在Ubuntu下可以直接登陆（Ubuntu下默认是不允许root登陆系统的），非常危险。


	$ sudo iptstate -C
