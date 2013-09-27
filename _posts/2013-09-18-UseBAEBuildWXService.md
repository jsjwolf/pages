---
layout: post
subject: '利用BAE搭建微信公号“小秘书”后台服务'
category: 'web'
keywords: '微信公共帐号,BAE'
sharing: true
comment: true
published: true
highlight: yes
description: '当你在公司、在家里抑或咖啡馆连上了一个免费AP，享受互联网冲浪无比畅快的同时，背后可能正有双眼睛在监视着，MM们正在臭美的QQ空间，GG们正浏览的"照片"，甚至是银行卡等帐号信息...这到底是怎么回事？'
---


##BAE搭建微信公共号

搭建mp.weixin认证服务参考：http://blog.sina.com.cn/s/blog_73b905910101a3xk.html
参照上面执行，微信开发者认证，下一步开启消息推送服务。默认的demo修改如下：

```
//$wechatObj->valid();
$wechatObj->responseMsg();
```

这时，给公众号发生任意非空内容，将收到回复“Welcome to wechat world!”。如果还是没有收到，或许是你开启了“编辑模式”。

##BAE 搭建微信公号自动响应服务
计费策略：<a href="http://developer.baidu.com/wiki/index.php?title=docs/cplat/billing">http://developer.baidu.com/wiki/index.php?title=docs/cplat/billing</a>

##增加翻译功能
###Build 翻译
http://www.cnblogs.com/mchina/p/3170565.html
baidu翻译文档：http://developer.baidu.com/wiki/index.php?title=帮助文档首页/百度翻译/翻译API

###youdao 翻译
http://fanyi.youdao.com/openapi?path=data-mode

##参考
google search (天气预报 上海 api) http://www.google.com.hk/search?newwindow=1&safe=strict&client=safari&rls=en&q=%E5%A4%A9%E6%B0%94%E9%A2%84%E6%8A%A5+%E4%B8%8A%E6%B5%B7+api&oq=%E5%A4%A9%E6%B0%94%E9%A2%84%E6%8A%A5+%E4%B8%8A%E6%B5%B7+api&gs_l=serp.12...0.0.0.83631886.0.0.0.0.0.0.0.0..0.0....0...1c..26.serp..0.0.0.RRVw2C1mR_w

<a href="http://www.weather.com.cn/data/cityinfo/101010100.html">Beijing</a>
<a href="http://www.weather.com.cn/data/cityinfo/101190401.html">Suzhou</a>

##backup
有道翻译API申请成功
API key：2122702772
keyfrom：goodbaiA
创建时间：2013-09-15
网站名称：goodbaiA
网站地址：http://goodbai.duapp.com

