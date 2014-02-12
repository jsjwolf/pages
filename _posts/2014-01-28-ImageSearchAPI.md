---
layout: post
subject: '可用的图片搜索（包括以图搜图）Web API全解析'
category: 'API'
keywords: '以图搜图,图片搜索,API '
sharing: true
comment: true
alias: 
published: true
highlight: yes
postimg_s: /images/artist/printer_200x133.jpg
description: '使用Google或Baidu的以图搜图搜索女神的头像，便可尽收网络上该女神的所有"隐私"...，以图搜图隐私问题突出，各家都没有公开公共API，但多想想还是有办法的。 '  
---

一般所指的图片搜索是通过关键字来搜索主题相关的图片，如搜索[MM](http://image.baidu.com/i?tn=baiduimage&ipn=r&ct=201326592&cl=2&lm=-1&st=-1&fm=result&fr=&sf=1&fmq=1392186127987_R&pv=&ic=0&nc=1&z=&se=1&showtab=0&fb=0&width=&height=&face=0&istype=2&ie=utf-8&word=%E7%BE%8E%E5%A5%B3);以图搜图则是指通过手头已有的图片来搜索与之相似或者相同的图片，你可能有[这一张图片](http://www.qianshou.com/uploads/photo/2010-10-22/2010102208553210.jpg)，不知道图片上的人是谁，你可以试试百度以图搜图[搜一下](http://image.baidu.com/i?ct=3&tn=shituresultpc&pn=0&rn=10&querysign=53254939,1926013523&q_txt=&rainbow=1&keyword=%E8%8B%8D%E4%BA%95%E7%A9%BA&shituRetNum=65&similarRetNum=600&faceRetNum=0&setnum=0&beautynum=0&imgurl=http://tutu2.baidu.com/1000/similar/20140212/14/53254939,1926013523.jpg)，该图片信息以及相关信息便一目了然。

本文包括三部分内容：
1. 以图搜图API分析 
2. 图片识别和图片处理API分析;
3. 通过关键字搜索相关图片API分析。

## 以图搜图 API 
以图搜图有下面几项用途：
1. 发现图片的来源与相关信息；
2. 研究追踪图片信息在互联网的传播；
3. 找到高分辨率版本的图片；
4. 找到有你照片的网页；
5. 看看这张图片有哪些不同版本。

三个主流以图搜图引擎如下：
1. [Google搜图](http://images.google.com.hk/imghp?hl=zh-CN);
2. [Baidu识图](http://stu.baidu.com/);
3. [TinEye](http://tineye.com/)，对国内图片收录不理想。

Google较保守，没有API可用;TinEye效果不理想；下面重点分析百度搜图API。  

虽然Baidu没有公开其搜图API，但经过潜心挖掘，API其实是可用的，而且还支持JSON数据格式，如下：  

**API形式1(参数tn可选faceresult/facejson)：**  
<http://stu.baidu.com/i?objurl=http%3A%2F%2Fimages.enet.com.cn%2F2008%2F1225%2F96%2F1995683.jpg&filename=&rt=0&rn=10&ftn=searchstu&ct=1&stt=1&tn=faceresult>    
**API形式2 (参数tn可选shituresult/facejson）：**  
<http://stu.baidu.com/i?ct=3&tn=shituresult&pn=0&rn=10&querysign=3046724883,3054654929&shituRetNum=8&similarRetNum=600&faceRetNum=1000&setnum=0&beautynum=0>     
**API形式3：**  
<http://stu.baidu.com/i?ct=3&tn=facejson&rn=6&querysign=3544027739,3922013179&shituRetNum=8&similarRetNum=20&faceRetNum=10&setnum=0&beautynum=0&stt=1&size_filter=-1&tab=0&pn=0&date_filter=0&width=&height=&ic=0&z=&sign=3046724883,3054654929>  


对形式1 API分析示例如下：<http://stu.baidu.com/i?objurl=http%3A%2F%2Fimages.enet.com.cn%2F2008%2F1225%2F96%2F1995683.jpg&filename=&rt=0&rn=10&ftn=searchstu&ct=1&stt=1&tn=facejson>   
```JSON
{
        "imgName":" ",
        "imgQuerySign":"1478561841,3033972892",
        "displayNum":1000,
        "listNum":1000,
        "rank":0,
        "beforeCt":3,
        "keyword":"",
        "data":[
            {
                "thumbURL":"http://t2.baidu.com/it/u=2299942138,954331452&fm=62&gp=0.jpg",
                "middleURL":"http://t2.baidu.com/it/u=2299942138,954331452&fm=62&gp=0.jpg",
                "largeTnImageUrl":"http://t2.baidu.com/it/u=2299942138,954331452&fm=62&gp=0.jpg",
                "hoverURL":"http://t2.baidu.com/it/u=2299942138,954331452&fm=62&gp=0.jpg",
                "faceURL":"http://t2.baidu.com/it/u=2792293033,3107780835&fm=61&gp=0.jpg",
                "hasLarge":0,
                "simi":"99",
                "objURLKey":"",
                "pageNum":0,
                "objURL":"http://img3.douban.com/view/photo/photo/public/p1221998136.jpg",
                "fromURL":"http://movie.douban.com/photos/photo/1221998136/",
                "fromURLEnc":"http://movie.douban.com/photos/photo/1221998136/",
                "fromURLHost":"http://movie.douban.com",
                "width":449,
                "height":600,
                "objId":255534948,
                "objType":"jpg",
                "time":"",
                "fileSize":"52",
                "fromPageTitle":
                "替身姐妹 第一季 剧照 ",
                "fromPageTitleEnc":"替身姐妹 第一季 剧照 ",
                "textHost":"替身姐妹 第一季的剧照 "
        },
        ......

        {}
        ]
}
~   
```
 
##图片识别和图片处理API
###faceplusplus 
[faceplusplus](http://www.faceplusplus.com.cn/)是北京一家专门做人脸处理技术的公司，被创新工厂投资，faceplusplus格式化人脸数据内容较丰富，提供的API也比较多，具体可以参考其[官方文档](http://cn.faceplusplus.com/dev/getting-started/core-concept/)。            

查询URL示例：<http://apicn.faceplusplus.com/v2/detection/detect?api_key=93fccb5a8b923956b0b6c5c8d416f0d1&api_secret=5ib4xNwJtOkWY_0JLSZye4eod33YhsT2&url=http://img.sucai.redocn.com/attachments/images/201204/20120417/Redocn_2012041310435227.jpg&attribute=glass,pose,landmark,gender,age,race,smiling>    
结果分析：
```JSON
{
    "face": [
        {
            "attribute": {
                "age": { "range": 5,  "value": 18 }, 
                "gender": { "confidence": 99.9997,  "value": "Female"  }, 
                "glass": {"confidence": 99.6323, "value": "None"  }, 
                "landmark": {
                    "left_eye_bottom": {  "x": 38.091224,   "y": 35.733333 }, 
                    "left_eye_center": {  "x": 37.96097,   "y": 34.152667 }, 
                    "left_eye_left_corner": { "x": 31.142725,    "y": 34.392  }, 
                    "left_eye_pupil": {  "x": 37.936028,  "y": 33.488833  }, 
                    "left_eye_right_corner": {  "x": 44.779215,   "y": 33.913167  }, 
                    "left_eye_top": {  "x": 37.607852,  "y": 31.502833 }, 
                    "left_eyebrow_left_corner": { "x": 27.275751,    "y": 29.799167  }, 
                    "left_eyebrow_right_corner": {   "x": 46.726559, 
                        "y": 29.188833
                    }, 
                    "mouth_left_corner": {
                        "x": 44.079677, 
                        "y": 57.167
                
......
                "pose": {
                    "pitch_angle": {
                        "value": 8.50726e-06
                    }, 
                    "roll_angle": {
                        "value": -0.609764
                    }, 
                    "yaw_angle": {
                        "value": 11
                    }
                }, 
                "race": {
                    "confidence": 88.9365, 
                    "value": "White"
                }, 
                "smiling": {
                    "value": 4.79256
                }
            }, 
            "face_id": "7493c03308095fa857ab2d1601e9fb40", 
            "position": {
                "center": {  "x": 52.078522, "y": 43.083333    }, 
                "eye_left": {   "x": 37.96097,      "y": 34.152667  }, 
                "eye_right": {  "x": 67.255889,  "y": 33.927667  }, 
                "height": 39.5, 
                "mouth_left": {  "x": 44.079677,   "y": 57.167 }, 
                "mouth_right": {  "x": 65.088915,    "y": 56.117167   }, 
                "nose": {   "x": 53.818938,  "y": 44.897333  }, 
                "width": 54.734411
            }, 
            "tag": ""
        }
    ], 
    "img_height": 830, 
    "img_id": "63d9362ea88e3c35c02b4ea8c79247d1", 
    "img_width": 600, 
    "session_id": "5bef10d1e65645548055fb3fd252f0a5", 
    "url": "http://img.sucai.redocn.com/attachments/images/201204/20120417/Redocn_2012041310435227.jpg"
}
```

### baidu 人脸API
百度人脸API，人脸格式化数据比Face++的少，也没有其他扩展的API（在其刚推出时试用过）。
[参考文档](http://developer.baidu.com/cloud/media)    
[人脸识别REST\ API帮助文档.pdf](http://bcs.duapp.com/cplat-01/mediacloud/face/%E4%BA%BA%E8%84%B8%E8%AF%86%E5%88%ABREST_API%E5%B8%AE%E5%8A%A9%E6%96%87%E6%A1%A3_v1.02.pdf)

申请baidu人脸识别API（新版管理平台中点击媒体云进入）：  
<http://developer.baidu.com/console#newmedia/face/service!appid=1306930>

申请通过在开发者网站首页->消息中心收到通知：

>应用《随手查查》的“人脸识别API”API权限申请对接成功
>时间：2013-10-17 16:22
>亲爱的开发者：
>
>恭喜您，应用《随手查查》的“人脸识别API”API权限申请信息对接成功，“人脸识别API”权限已开启，请前往 控制台-高蛋白助手-API管理 查看。
>
>百度开发者中心


Baidu BAE控制台API管理[页面](http://developer.baidu.com/dev#/api/1306930/list) ,API TOKEN获取：[API](https://openapi.baidu.com/oauth/2.0/token?grant_type=client_credentials&client_id=ANGEgE28iVZYfWqOY80ih0Az&client_secret=5hmFiSRasZgysAca6ExIdv89BfTyG2RW&)

```json
{
"expires_in":2592000,
"refresh_token":"4.ab4354e2545382b48013925c5c200b73.315360000.1697428984.282335-1306930",
"access_token":"3.59af4cc25f0c6397b9013dd0b0d60638.2592000.1384660984.282335-1306930",
"session_secret":"f3999ceb76fbbd86d0806ff9b29a81b7",
"session_key":"94rmKleH9+FtnYAQbkzv41BxHhIrh5U7BCNykDa0H\/Bf7AKYaywg6AEYYcfyGTJ0xIp76EER6axW1CcKrPY+2ZUrFVo=",
"scope":"public wise_adapt media_face"
}
```
scope: <http://developer.baidu.com/wiki/index.php?title=docs/oauth/list>  

请求示例: <https://openapi.baidu.com/rest/2.0/media/v1/face/detect?access_token=3.59af4cc25f0c6397b9013dd0b0d60638.2592000.1384660984.282335-1306930&url=http://c.hiphotos.bdimg.com/album/s%3D800/sign=6bd70c1da044ad342abf8a87e0a00c08/8d5494eef01f3a29d7017b3e9825bc315d607c71.jpg>  

注意:query string 需要先进行 urlencode

```JSON
{
"face":[{  //在原图片中检测到的人脸数组
    "face_id":"e4fbc65ac9ea4135a9c4cda0294ccd5f",
    "attribute":{
        "gender":{"confidence":"0.558445","value":"male"},  //检测到的人脸的性别及置信度
        "smiling":{"confidence":"0.140648"},  //检测到的笑脸及置信度
        "face":{"value":"true","confidence":"1"} / /？
    },
    "position":{ //检测到的人脸的位置,坐标均进行了归一化,值在 0~1 之间，中心坐标
        "center":{"x":"0.389400","y":"0.403700"},/ /人脸区域中心 坐标
        "width":"0.353520",  //人脸区域宽
        "height":"0.243930", //人脸区域宽
        "eye_left":{"x":"0.284900","y":"0.354970"},
        "eye_right":{"x":"0.449460","y":"0.344870"},
        "mouth":{"x":"0.373490","y":"0.482450"}
    }
}],
"img_id":"2b0e035557a04ad49e6f054f2556e4b6",
"url":"http:\/\/c.hiphotos.bdimg.com\/album\/s=800\/sign=6bd70c1da044ad342abf8a87e0a00c08\/8d5494eef01f3a29d7017b3e9825bc315d607c71.jpg",  //请求中的图片 url
"session_id":"16d2e9271173dc7c307b7cd2be4328e5",  //唯一标识请求，目前好像没有价值
"img_width":"138",  
"img_height":"200"  //图片宽度
}
```


## 通过关键字搜索相关图片API

### baidu 图片搜索API（非以图搜图）  
大部分的网民都喜欢看图片胜过看文字，尤其是美女这类信息，图片来的更为直接一些。

所以图片搜索就变得非常重要了。

百度图片搜索官方并没有开放API出来，但我们可以直接使用它的ajax请求网址作为api使用，因为它返回的数据格式是json格式的，它是非常通用的数据格式。

百度图片搜索API地址] 
<http://image.baidu.com/i?tn=baiduimagejson&ct=201326592&cl=2&lm=-1&st=-1&fm=result&fr=&sf=1&fmq=1349413075627_R&pv=&ic=0&nc=1&z=&se=1&showtab=0&fb=0&width=&height=&face=0&istype=2&word=girl&rn=2&pn=1>  


主要参数：
word，查询关键词
rn，每页显示图片数量
pn，图片显示的页码


其它参数照抄上述api地址中的就行。

##参考：
<http://2sitebbs.com/thread-695-1-1.html>
