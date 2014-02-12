---
layout: post
subject: '百科_翻译_黄历API分析和常用符号表'
category: 'API'
keywords: '百科,翻译,黄历,符号表,API'
sharing: true
comment: true
alias: 
published: true
highlight: yes
postimg_s: /images/artist/printer_200x133.jpg
description: ' 主要介绍百度百科API使用方法，有道翻译API使用方法和老黄历API使用，另外再附一个有用的符号表。'  
---

微信公共号提供百科、中英翻译以及老黄历查询，是一件很有意思也有的事。以前与朋友聊天听到一个新的网络词汇，只能一头雾水，现今你可以轻松掏出手机，向微信公共号『随手查查』回复”白富美“（这里是例举），就一切明白了。

本文主要介绍：
1. 百度百科API使用方法;
2. 有道翻译API使用方法;
3. 老黄历API方案;
4. 个人收集的常用符号表。

中文百科网站主要有三家，[百度百科](http://baike.baidu.com/link?url=C1JNg9D7uiwZ1U02QUuhMzpnHM_knIaPNC-Uqj5CsrP_39qb8kzE410j8gke5tOoienLhhWrYnqLkb3HhiUQua)、[维基百科](http://zh.wikipedia.org/w/index.php?search=%E7%99%BD%E5%AF%8C%E7%BE%8E&title=Special%3A%E6%90%9C%E7%B4%A2)和[互动百科](http://www.baike.com/wiki/%E7%99%BD%E5%AF%8C%E7%BE%8E&prd=button_doc_jinru)。  

## 维基百科API分析（wikipedia）
没有详细分析。  
IBM Blog参考：<http://www.ibm.com/developerworks/cn/xml/x-phpwikipedia/>   
形如：<http://git.oschina.net/goodbai/goodbai-wiki/wikis/pages>  
API Ref: <http://www.mediawiki.org/wiki/API>  

##互动百科API分析
当前没有研究。

##百度百科API分析
API示例：<http://baike.baidu.com/api/openapi/BaikeLemmaCardApi?scope=103&format=json&appid=379020&bk_key=%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F&bk_length=600>

**JSON数据格式分析**  
该接口默认返回的数据是经escape() 函数编码的（escape可对字符串进行编码，这样就可以在所有的计算机上读取该字符串），相应使用unescape函数可解码（参考：http://www.w3school.com.cn/js/jsref_escape.asp）。  

下面是“操作系统”关键词查询返回的结果：
```JSON
{       
        "id":"880",
        "subLemmaId":"4940471",
        "key":"\u64cd\u4f5c\u7cfb\u7edf",                    //查询词
        "title":"\u64cd\u4f5c\u7cfb\u7edf",     
        "card":[{
                "key":"m25_nameC",
                "name":"\u4e2d\u6587\u540d",                //中文名称
                "value":["\u64cd\u4f5c\u7cfb\u7edf"],
                "format":["\u64cd\u4f5c\u7cfb\u7edf"]
        },
        {
                "key":"m25_nameE",
                "name":"\u5916\u6587\u540d",                //英文名称
                "value":["Operating System<\/a>"],
                "format":["Operating System<\/a>"]
        },
        {
                "key":"m25_ext_0",
                "name":"\u82f1\u6587\u7b80\u79f0",       //英文简称
                "value":["OS<\/a>"],
                "format":["OS<\/a>"]
        },
        {
                "key":"m25_ext_1",
                "name":"\u7ec4\u6210\u90e8\u5206",     //组成部分
                "value":["\u5185\u6838<\/a>\u3001\u9a71\u52a8\u7a0b\u5e8f<\/a>\u3001\u63a5\u53e3\u5e93\u3001\u5916\u56f4"],
                "format":["\u5185\u6838<\/a>\u3001\u9a71\u52a8\u7a0b\u5e8f<\/a>\u3001\u63a5\u53e3\u5e93\u3001\u5916\u56f4"]
        },
        {
                "key":"m25_ext_2",
                "name":"\u5e38\u89c1\u7cfb\u7edf",          //常见系统
                "value":["Android<\/a>, iOS<\/a>, Linux<\/a>, Windows<\/a>"],"format":["Android<\/a>, iOS<\/a>, Linux<\/a>, Windows<\/a>"]
        }],
        "image":"http:\/\/imgsrc.baidu.com\/baike\/pic\/item\/f11f3a292df5e0fedeceedfa5c6034a85fdf7272.jpg",
        "imageHeight":"400",
        "imageWidth":"450",
        "isSummaryPic":"y",
 //介绍       "abstract":"\u64cd\u4f5c\u7cfb\u7edf\u662f\u7ba1\u7406\u8ba1\u7b97\u673a\u786c\u4ef6\u8d44\u6e90\uff0c\u63a7\u5236\u5176\u4ed6\u7a0b\u5e8f\u8fd0\u884c\u5e76\u4e3a\u7528\u6237\u63d0\u4f9b\u4ea4\u4e92\u64cd\u4f5c\u754c\u9762\u7684\u7cfb\u7edf\u8f6f\u4ef6\u7684\u96c6\u5408\u3002\u64cd\u4f5c\u7cfb\u7edf\u662f\u8ba1\u7b97\u673a\u7cfb\u7edf\u7684\u5173\u952e\u7ec4\u6210\u90e8\u5206\uff0c\u8d1f\u8d23\u7ba1\u7406\u4e0e\u914d\u7f6e\u5185\u5b58\u3001\u51b3\u5b9a\u7cfb\u7edf\u8d44\u6e90\u4f9b\u9700\u7684\u4f18\u5148\u6b21\u5e8f\u3001\u63a7\u5236\u8f93\u5165\u4e0e\u8f93\u51fa\u8bbe\u5907\u3001\u64cd\u4f5c\u7f51\u7edc\u4e0e\u7ba1\u7406\u6587\u4ef6\u7cfb\u7edf\u7b49\u57fa\u672c\u4efb\u52a1\u3002\u64cd\u4f5c\u7cfb\u7edf\u7684\u79cd\u7c7b\u5f88\u591a\uff0c\u5404\u79cd\u8bbe\u5907\u5b89\u88c5\u7684\u64cd\u4f5c\u7cfb\u7edf\u53ef\u4ece\u7b80\u5355\u5230\u590d\u6742\uff0c\u53ef\u4ece\u624b\u673a\u7684\u5d4c\u5165\u5f0f\u64cd\u4f5c\u7cfb\u7edf\u5230\u8d85\u7ea7\u8ba1\u7b97\u673a\u7684\u5927\u578b\u64cd\u4f5c\u7cfb\u7edf\u3002\u76ee\u524d\u6d41\u884c\u7684\u73b0\u4ee3\u64cd\u4f5c\u7cfb\u7edf\u4e3b\u8981\u6709Android\u3001BSD\u3001iOS\u3001Linux\u3001Mac OS X\u3001Windows\u3001Windows Phone\u548cz\/OS\u7b49\uff0c\u9664\u4e86Windows\u548cz\/OS\u7b49\u5c11\u6570\u64cd\u4f5c\u7cfb\u7edf\uff0c\u5927\u90e8\u5206\u64cd\u4f5c\u7cfb\u7edf\u90fd\u4e3a\u7c7bUnix\u64cd\u4f5c\u7cfb\u7edf\u3002",
        "catalog":["\u7b80\u4ecb<\/a>","\u53d1\u5c55\u5386\u53f2<\/a>","\u7ec4\u6210\u90e8\u5206<\/a>","\u5185\u6838\u7ed3\u6784<\/a>"],  //章节标题
        "url":"http:\/\/baike.baidu.com\/view\/880.htm",
        "hasOther":"1",
        "subLemma":"",
        "logo":"http:\/\/img.baidu.com\/img\/baike\/logo-baike.gif",
 //百度版权声明       "copyrights":"\u4ee5\u4e0a\u5185\u5bb9\u6765\u81ea\u767e\u5ea6\u767e\u79d1\u5e73\u53f0\uff0c\u7531\u767e\u5ea6\u767e\u79d1\u7f51\u53cb\u521b\u4f5c\u3002"
}

```

查询出错示例如下：[查看原始页面](http://baike.baidu.com/api/openapi/BaikeLemmaCardApi?scope=103&format=json&appid=379020&bk_key=%E6%88%91%E4%BB%AC%E9%83%BD%E6%98%AF%E4%B8%AD%E5%9B%BD%E4%BA%BA%E7%9A%84%E5%9B%9B%20%20%20%E5%A4%A7%E6%9B%B4%E5%A4%9A%E9%98%BF%E8%BE%BE%E4%B8%89%E4%B8%AA&bk_length=600)
```
{"error_code":"20000","error_msg":"search word not found"}
```


##翻译功能
###百度翻译

百度翻译内容比较简单，中译英或英译中结果一般只有一个词（或一句话），没有音标、相近词、用法等信息。  
一篇参考：<http://www.cnblogs.com/mchina/p/3170565.html>  
baidu翻译官方：<http://developer.baidu.com/wiki/index.php?title=帮助文档首页/百度翻译/翻译API>

###有道翻译
有道翻译内容比较丰富，既有释意，也有音标、相近词、用法等信息。  
官方页面：<http://fanyi.youdao.com/openapi?path=data-mode>  
API示例：<http://fanyi.youdao.com/fanyiapi.do?keyfrom=goodbaiA&key=2122702772&type=data&doctype=json&version=1.1&q=翻译>  
```
{
    "translation":["translation"],
    "basic":{
        "phonetic":"fān yì",
        "explains":["translate","interpret"]
    },
    "query":"翻译",
    "errorCode":0,
    "web":[
        {"value":["Translation","translate","Translator"],"key":"翻译"},
        {"value":["Machine translation","mechanical translation","Machinery-Translating"],"key":"机器翻译"},
        {"value":["Translation memory","Translation memories","Translation memory manager"],"key":"翻译记忆"} 
    ]
}

```
示例二  

```

{
    "translation":["狼"],
    "basic":{
        "phonetic":"wʊlf",
        "explains":["n. 狼；色狼；残忍贪婪之人","vt. 大吃；狼吞虎咽地吃"]
    },
    "query":"wolf",
    "errorCode":0,
    "web":[
        {"value":["狼","沃尔夫","狼与美女"],"key":"wolf"},
        {"value":["艾瑞克·沃尔夫","沃尔夫","伍尔夫"],"key":"Eric Wolf"},
        {"value":["埃塞俄比亚狼","衣索比亚狼","西门豺"],"key":"Ethiopian wolf"}
    ]
}

```

## 农历的实现
农历和公历间可通过算法对应，参考该项目实现：<https://github.com/dfar2008/c3crm/blob/4f68a942f28ca935439a9b34e9834819a4726f1c/modules/Memdays/Lunar.php>

###汉典老黄历API  
[汉典](http://www.zdic.net/appendix/f27.htm)，其提供老黄历chm文件下载，其他资源也不错（包括字典，词典等）。
分析其老黄历访问接口：<http://www.zdic.net/nongli/inc/ll2.asp>  
汉典老黄历API：<http://www.zdic.net/nongli/2014-10-14.htm> （这个接口不错）  

## 附录1：参考资料
收集一些特殊的符号: <http://dev.yesky.com/89/33282589.shtml>
百度百科API参考：<https://github.com/yansunrong/baike/blob/master/content_scripts/baike.js>  


## 附录2：一些有意思的符号集合
通过组合符号等方法，可实现创意表达方法，微信可以很好的识别下面的符号，而且还对一些符号作了夸张的修饰。
如：
```
                   ,　-----　､ 
　　　　　　　　____／〃"　　､､､　ヽ 
　　　　　　∠＞　｀￣￣｀ヽ, --z _ i 
　　　　 ／ ,＞　　　　　　　　　　 ヽ ￣＼ 
　 　　./,∠. 　　　人　 ､　　　゛゛゛　ヽ゛､ ヽ 
　　　/　_ノ .　　/ 　|　/|人　　゛゛゛　|　　　i 　　　＿＿＿＿＿ 
　　　|　　〉/　./|　　ﾚ´／　｀ヽ λ　│ |　|i | 　／ 
　　　i ll 〈/| 　|‐ヽ　　 , ― ､　|ﾉ | 　丿| |┤| ＜　人は死ぬぞ 
　 　 |　　　|人′・ﾉ 　　､・_丿　/ヽ,ノ ﾚ ､/ 　　＼＿＿＿＿＿ 
　　　ゞ､ ゛　 　|　ヽ　　　　, メ　б│､|　"/ 
　　　　 ＼゛､　ヽ　　ー　　　　 ゝ.ノ "　 ／ 
　　　　　　｀‐ヽ _＼ _　＿　ノ ／ﾚ'__／ 
　　　 　 　　　　　　 　|　 　　.| 
　　　　　　　　　______.ﾉ 　 　 人(⌒) 
　　　　　　　 ／/::::::::|-､ ,-/::::::ﾉ ~.ﾚ-r┐ 
　　　　　　 /　/:::::::::::|　　/:::::ノ__　|　.|　ﾄ､ 
　　　　　　 | /:::::::::::::::|　〈￣ 　　｀-Lλ_ﾚ′ 
　　　　　　 ﾚ::::::::::::::::::|/:::￣｀ー‐---‐′ 

```
部分符号集合  
```
、。·ˉˇ¨〃々—～‖…‘’“”〔〕〈 〉《》「」『』〖〗【】±+-×÷∧∨∑∏∪∩∈√⊥∥∠⌒⊙∫∮≡≌≈∽∝≠≮≯≤≥∞∶ ∵∴∷♂♀°
′″℃$¤￠￡‰§№☆★〇○●◎◇◆ 回□■△▽⊿▲▼◣◤◢◥▁▂▃▄▅▆▇█▉▊▋▌▍▎▏▓※→←↑↓↖↗↘↙〓 ⅰⅱⅲⅳⅴⅵⅶⅷⅸⅹ①②③④⑤⑥⑦⑧⑨⑩
⒈⒉⒊⒋ ⒌⒍⒎⒏⒐⒑⒒⒓⒔⒕⒖⒗⒘⒙⒚⒛⑴⑵⑶⑷⑸⑹⑺⑻⑼⑽⑾⑿⒀⒁⒂⒃⒄⒅⒆⒇㈠㈡㈢㈣㈤㈥㈦㈧㈨㈩
ⅠⅡⅢⅣⅤⅥⅦⅧⅨⅩⅪⅫ!"#￥%&'()*+，-./0123456789：;<=>？@ABCDEFGHIJLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|｝
ぁあぃいぅうぇえぉおかがきぎくぐけげこごさざしじすずせぜそぞただちぢっつづてでとどなにぬねのはばぱひびぴふぶぷ
へべぺほぼぽまみむめもゃやゅゆょよらりるれろゎわゐゑをんァアィイゥウェエォオカガキギクグケゲコゴ
サザシジスズセゼソゾタダチヂッツヅテデトドナニヌネノハバパヒビピフブ
プヘベペホボポマミムメモャヤュユョヨラリルレロヮワヰヱヲンヴヵヶΑΒΓΔΕΖΗΘΙΚΛΜΝΞΟΠΡΣΤΥΦΧΨΩαβγδεζηθ ικλμνξοπρστυφχψ 
ω︵︶︹︺︿﹀︽︾﹁﹂﹃﹄︻︼︷︸АБВГДЕЁЖЗИЙКЛМНОПРСТУФХЦЧШЩЪЫЬЭЮЯабвгдеёжзийклмнопрстуфхцчшщъыь эюāáǎàēéěèī 
íǐìōóǒòūúǔùǖǘǚǜüêɑńňɡㄅㄆㄇㄈㄉㄊㄋㄌㄍㄎㄏㄐㄑㄒㄓㄔㄕㄖㄗㄘㄙㄚㄛㄜㄝㄞㄟㄠㄡㄢㄣㄤㄥㄦㄧㄨㄩ︱︳︴﹏﹋﹌─━│┃┄┅┆ 
┇┈┉┊┋┌┍┎┏┐┑┒┓└┕┖┗┘┙┚┛├┝┞┟┠┡┢┣┤┥┦┧┨┩┪┫┬┭┮┯┰┱┲┳┴┵┶┷┸┹┺┻┼┽┾┿╀╁╂╃╄ ╅╆╇╈╉╊╋？㊣㈱曱甴囍∟┅﹊﹍╭ ╮╰ ╯ _ ^︵^﹕﹗
/\ " < > `,·。{}~～() -√ $ @ * & # 卐℡ ぁ〝〞ミ灬№*ㄨ≮≯ ﹢﹣/∝≌∽≦≧≒﹤﹥じぷ┗┛￥￡§я-―‥…‰′″℅℉
№℡∕∝∣═║╒╓╔╕╖╗╘╙╚╛╜╝╞╟╠╡╢╣╤╥╦╧╨╩╪╫╬╱ ╲╳▔▕〆〒〡〢〣〤〥〦〧〨〩㎎ ㎏ ㎜ ㎝ ㎞ ㎡ ㏄ ㏎㏑㏒㏕
兀︰﹍﹎ ------
```
