---
layout: post
subject: '天气预报及环境指数开放API综合分析'
category: 'Web'
tags: '天气预报接口 PM2.5 PM10 API'
keywords: '天气预报接口,PM2.5,PM10,API'
sharing: true
comment: true
alias: 
published: true
highlight: yes
postimg_s: /images/artist/forecast_200x133.jpg
description: '因微信公共号提供城市天气预报服务，发现网山天气预报接口和环境数据接口方面资料比较乱，而且有不少内容已经失效，下面内容由过程日志整理形成，希望有人可用得上。'
---
因微信公共号提供城市天气预报服务，发现网山天气预报接口和环境数据接口方面资料比较乱，而且有不少内容已经失效，下面内容由过程日志整理形成，希望有人可用得上。


##一 天气预报接口分析

国内的天气预报**数据**，主要是国家气象局和国外两个来源（指数据来源）。  

+ **国外**方面主要是雅虎（XML格式，谷歌已经停掉了自家的天气API）；  
+ **国内**数据由国家气象局提供（JSON格式）。  

在知乎看到过一篇专业人士评论，雅虎可以提供全球范围的且非常精细的天气数据（小时级更新），但主要是通过计算机自动生成。而国家气象局则是各地经验丰富的专家人士结合计算机综合分析得出，在准确性上更胜一筹。我就这样信了！  

###国家气象局天气接口

气象局的天气预报主页：[移动版](http://mobile.weather.com.cn/city/101020100.html) [普通版](http://www.weather.com.cn/) ,仔细挖掘这两个地址可以发现很多好东西哦。    

**特点：接口多，来自weather.com.cn和mobile.weather.com.cn都有，次数不限制，服务稳定，功能全面，优先推荐。**


####1. 实时天气接口 
有多个接口，好像差别不大：  
实时天气1：<http://www.weather.com.cn/data/sk/101190408.html>  
实时天气2：<http://www.weather.com.cn/data/cityinfo/101190408.html>  
实时天气3（带时间戳）：<http://mobile.weather.com.cn/data/sk/101010100.html?_=1381891661455>

####2. 一周天气预报接口 

* 7天预报数据
URL： <http://mobile.weather.com.cn/data/forecast/101010100.html?_=1381891660081>   
该接口来源气象局移动版网站，目测数据最为精确，详细接口格式如下：      
	 
	//7天预报数据
	{"c":{"c1":"101010100","c2":"beijing","c3":"北京","c4":"beijing","c5":"北京","c6":"beijing","c7":"北京","c8":"china","c9":"中国","c10":"1","c11":"010","c12":"100000","c13":"116.391","c14":"39.904","c15":"33","c16":"AZ9010","c17":"+8"},
	"f":{"f1":
	    [
	    {"fa":"01","fb":"03","fc":"10","fd":"5","fe":"0","ff":"0","fg":"0","fh":"0","fi":"06:21|17:40"}, 
	    {"fa":"07","fb":"07","fc":"19","fd":"12","fe":"0","ff":"0","fg":"0","fh":"0","fi":"06:22|17:38"},
	    {"fa":"02","fb":"00","fc":"15","fd":"5","fe":"8","ff":"8","fg":"3","fh":"1","fi":"06:23|17:37"},
	    {"fa":"00","fb":"00","fc":"16","fd":"4","fe":"0","ff":"0","fg":"0","fh":"0","fi":"06:24|17:35"},
	    {"fa":"00","fb":"00","fc":"18","fd":"7","fe":"0","ff":"0","fg":"0","fh":"0","fi":"06:25|17:34"},
	    {"fa":"00","fb":"01","fc":"18","fd":"8","fe":"0","ff":"0","fg":"0","fh":"0","fi":"06:26|17:32"},
	    {"fa":"01","fb":"01","fc":"16","fd":"6","fe":"0","ff":"0","fg":"0","fh":"0","fi":"06:27|17:31"}],
	"f0":"201310121100"}}
 
详细接口分析如下：  
 
	//格式说明
	var format={fa:图片1,fb：图片2,fc:温度1,fd：温度2,fe:风向1,ff：风向2,fg:风力1,fh：风力2,fi:日出日落};  
	//定义天气类型
	var weatherArr={"00":"晴","01":"多云","02":"阴","03":"阵雨","04":"雷阵雨","05":"雷阵雨伴有冰雹","06":"雨夹雪","07":"小雨","08":"中雨","09":"大雨","10":"暴雨","11":"大暴雨","12":"特大暴雨","13":"阵雪","14":"小雪","15":"中雪","16":"大雪","17":"暴雪","18":"雾","19":"冻雨","20":"沙尘暴","21":"小到中雨","22":"中到大雨","23":"大到暴雨","24":"暴雨到大暴雨","25":"大暴雨到特大暴雨","26":"小到中雪","27":"中到大雪","28":"大到暴雪","29":"浮尘","30":"扬沙","31":"强沙尘暴","53":"霾","99":""};  
	//定义风向数组  
	var fxArr={"0":"无持续风向","1":"东北风","2":"东风","3":"东南风","4":"南风","5":"西南风","6":"西风","7":"西北风","8":"北风","9":"旋转风"};    
	//定义风力数组  
	var flArr={"0":"微风","1":"3-4级","2":"4-5级","3":"5-6级","4":"6-7级","5":"7-8级","6":"8-9级","7":"9-10级","8":"10-11级","9":"11-12级"};  
	 

* 6天预报数据
URL： <http://m.weather.com.cn/data/101190408.html>   
该接口来源网络，应该是一个流传比较久的接口，大发现准确性和稳定性可能不稳定。接口分析如下：

	{"weatherinfo":{
		#基本信息
		"city":"北京","city_en":"beijing","date_y":"2013年9月24日","date":"","week":"星期二","fchh":"11","cityid":"101010100",
		#6天温度范围
		"temp1":"22℃~11℃","temp2":"25℃~11℃","temp3":"26℃~13℃","temp4":"25℃~15℃","temp5":"25℃~16℃","temp6":"24℃~16℃",
		#6天温度范围（华氏）
		"tempF1":"71.6℉~51.8℉","tempF2":"77℉~51.8℉","tempF3":"78.8℉~55.4℉","tempF4":"77℉~59℉","tempF5":"77℉~60.8℉","tempF6":"75.2℉~60.8℉",
		#6天天气
		"weather1":"多云转晴","weather2":"晴","weather3":"晴","weather4":"晴转多云","weather5":"阴转阵雨","weather6":"多云",
		#6天天气图示（每天2张）
		"img1":"1","img2":"0","img3":"0","img4":"99","img5":"0","img6":"99","img7":"0","img8":"1","img9":"2","img10":"3","img11":"1","img12":"99",
		"img_single":"1",
		#6天天气图示文字（每天2）
		"img_title1":"多","img_title2":"晴","img_title3":"晴","img_title4":"晴","img_title5":"晴","img_title6":"晴","img_title7":"晴","img_title8":"多云","img_title9":"阴","img_title10":"阵雨","img_title11":"多云","img_title12":"多云",
		"img_title_single":"多云",
		#6天风
		"wind1":"北风3-4级转微风","wind2":"微风","wind3":"微风","wind4":"微风","wind5":"微风","wind6":"微风",
		"fx1":"北风","fx2":"微风",
		#6天风力情况
		"fl1":"3-4级转小于3级","fl2":"小于3级","fl3":"小于3级","fl4":"小于3级","fl5":"小于3级","fl6":"小于3级",
		#24小时内
		"index":"较冷","index_d":"建议着大衣、呢外套加毛衣、卫衣等服装。体弱者宜着厚外套、厚毛衣。因昼夜温差较大，注意增减衣服。",
		#48小时内
		"index48":"较舒适","index48_d":"建议着薄外套或牛仔衫裤等服装。年老体弱者宜着夹克衫、薄毛衣等。昼夜温差较大，注意适当增减衣服。",
		#紫外 洗车 旅游 舒适指数 晨练 晾晒 过敏
		"index_uv":"弱",
		"index48_uv":"强",
		"index_xc":"适宜",
		"index_tr":"适宜",
		"index_co":"舒",
		"st1":"21","st2":"12","st3":"24","st4":"12","st5":"25","st6":"14", #不明
		"index_cl":"较适宜",
		"index_ls":"适宜",
		"index_ag":"极易发"
	}}
 

####3. 相应图片信息接口 
不同天气对应不同图片，每天两张图片对应白天和晚上天气，下面以晴天为例演示几种可用的图片类型。 
 
来自weather.com.cn的图片： 
20x20：<http://m.weather.com.cn/img/c0.gif>       
50x46：<http://m.weather.com.cn/img/b0.gif>  
29x20：<http://www.weather.com.cn/m/i/weatherpic/29x20/d0.gif>    #白天带黄色背景  
29x20：<http://www.weather.com.cn/m2/i/icon_weather/29x20/n00.gif> #夜间带深色背景  
城市LOGO：<http://poster.weather.com.cn/p_files/base/101020100.jpg>

c打头的图片是20*20像素的，b打头的是50*46像素的，d打头的是反白的图标，29*20像素，n打头的是夜间反白图标，29*20像素，注意这里的文件名是两位数字！

来自mobile.weather.com的图片(图片看起来更美观）)：  
晴天：<http://mobile.weather.com.cn/images/day/00.png>   
多云：<http://mobile.weather.com.cn/images/day/01.png>   
多云晚上： <http://mobile.weather.com.cn/images/night/01.png>  
下雨：<http://mobile.weather.com.cn/images/day/07.png>   
阴天：<http://mobile.weather.com.cn/images/day/02.png>   
    
####4. 其他数据获取接口
天气资讯数据： <http://mobile.weather.com.cn/data/news/khdjson.htm?_=1381891660018>  
室外活动指数数据：<http://mobile.weather.com.cn/data/zsM/101010100.html?_=1381891661502>  
3H预测数据接口： <http://mobile.weather.com.cn/data/forecast3h/101010100.html?_=1381891661605> 

如有特殊天气发生，通过预警接口可获取预警数据，详细如下：   
特殊天气预警接口：<http://product.weather.com.cn/alarm/stationalarm.php?areaid=101190401&count=-1&_=1381894097508>  
信息格式如下：  
 
	var alarminfo={"count":"25","data":[["江苏省","**10119-20131204220706-1203.html**"]]};
 
预警内容获取：<http://www.weather.com.cn/data/alarm/10119-20131204220706-1203.html>  
预警信息页面：<http://mobile.weather.com.cn/yj_content.html?file=10119-20131204220706-1203.html>  
 
	var alarminfo={
	    "ALERTID":"201312042208582381大雾橙色",
	    "PROVINCE":"江苏省",
	    "CITY":"","STATIONNAME":"",
	    "SIGNALTYPE":"大雾",
	    "SIGNALLEVEL":"橙色",
	    "ISSUETIME":"2013-12-04 22:07:06",
	    "ISSUECONTENT":"江苏省气象台13年12月4日21时57分发布大雾橙色预警信号。目前淮安、盐城、南通、扬州和泰州的大部分地区已经出现能见度小于200米的雾并将持续，另外预计未来12小时内徐州、连云港、宿迁、南京、镇江、常州、无锡和苏州的大部分地区将出现能见度小于1000米、大于等于200米的雾。",
	    "UNDERWRITER":"魏建苏",
	    "RELIEVETIME":"2013-12-05 22:07:06"
	}
 

####5. 获取全国所有城市代码列表
**方法一**  
XML接口根节点： <http://flash.weather.com.cn/wmaps/xml/china.xml>  
XML接口主要作用是递归获取全国几千个县以上单位的城市代码，如：  
江苏的XML地址为：<http://flash.weather.com.cn/wmaps/xml/shanghai.xml>   
苏州的XML地址为：<http://flash.weather.com.cn/wmaps/xml/jiangsu.xml>  
上面页面获得太仓city code：101190408  
合成太仓天气信息地址：<http://m.weather.com.cn/data/101190408.html>

下面贴一段[PHP代码](https://github.com/jsjwolf/good_utils/blob/master/getAllCityCode4Weather_com.php)实现的，通过XML接口根节点递归获得全国几千个县以上城市cide code的代码，供参考(可直接在终端下运行）：

**方法二**  
更新：最新发现可以一次性获取全国+国外主要城市，8763个城市列表信息。  
URL：<http://mobile.weather.com.cn/js/citylist.xml>  

###Yahoo天气预报接口
URL: <http://weather.yahooapis.com/forecastrss?w=2151330&u=c>  
该接口没有测试。

*****************************************
*****************************************

##二 环境指数接口
最近大半个中国发生的严重雾霾天气，震惊了我们的老百姓，PM2.5指数概念深入人心。目前有两个接口推荐：  

 * pm25.in，需要申请KEY,次数有限制，数据内容丰富，格式清楚；
 * 国家气象局，无需KEY，且次数无限制，数据内容简单，无PM10数据。


### pm25.in PM10/PM2.5数值查询接口
[pm25.in主页](pm25.in：http://www.pm25.in/api_doc)，pm25.in是一家第三方提供pm2.5/pm10环境数据的网站，数据来源于中国国家环境监测中心，其接口使用需要申请KEY（具体请参考其官方网站）。  
  
PM10调用：<http://www.pm25.in/api/querys/pm10.json?city=%E4%B8%8A%E6%B5%B7&token=5j1znBVAsnSf5xQyNQyq&avg>(该链接中的Key仅供测试用，请自行申请）    
数据格式如下：（**最后一行为所有检测点平均值**）
 
	[
	{"aqi":147,"area":"上海","pm10":180,"pm10_24h":167,"position_name":"普陀","primary_pollutant":"颗粒物(PM2.5)","quality":"轻度污染","station_code":"1141A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":163,"area":"上海","pm10":151,"pm10_24h":156,"position_name":"十五厂","primary_pollutant":"颗粒物(PM2.5)","quality":"中度污染","station_code":"1142A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":150,"area":"上海","pm10":0,"pm10_24h":0,"position_name":"虹口","primary_pollutant":"颗粒物(PM2.5)","quality":"轻度污染","station_code":"1143A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":170,"area":"上海","pm10":0,"pm10_24h":0,"position_name":"徐汇上师大","primary_pollutant":"颗粒物(PM2.5)","quality":"中度污染","station_code":"1144A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":0,"area":"上海","pm10":0,"pm10_24h":0,"position_name":"杨浦四漂","primary_pollutant":null,"quality":null,"station_code":"1145A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":79,"area":"上海","pm10":115,"pm10_24h":108,"position_name":"青浦淀山湖","primary_pollutant":"颗粒物(PM10)","quality":"良","station_code":"1146A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":160,"area":"上海","pm10":0,"pm10_24h":121,"position_name":"静安监测站","primary_pollutant":"颗粒物(PM2.5)","quality":"中度污染","station_code":"1147A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":150,"area":"上海","pm10":198,"pm10_24h":135,"position_name":"浦东川沙","primary_pollutant":"颗粒物(PM2.5)","quality":"轻度污染","station_code":"1148A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":152,"area":"上海","pm10":152,"pm10_24h":150,"position_name":"浦东新区监测站","primary_pollutant":"颗粒物(PM2.5)","quality":"中度污染","station_code":"1149A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":0,"area":"上海","pm10":0,"pm10_24h":0,"position_name":"浦东张江","primary_pollutant":null,"quality":null,"station_code":"1150A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":146,"area":"上海","pm10":159,"pm10_24h":139,"position_name":null,"primary_pollutant":null,"quality":"轻度污染","station_code":null,"time_point":"2013-12-04T19:00:00Z"}
	]
 

PM2.5调用：<http://www.pm25.in/api/querys/pm2_5.json?city=%E4%B8%8A%E6%B5%B7&token=5j1znBVAsnSf5xQyNQyq&avg>**(该链接中的Key仅供测试用，请自行申请）**  
数据格式如下：（**最后一行为所有检测点平均值**）  
 
	[
	{"aqi":147,"area":"上海","pm2_5":105,"pm2_5_24h":112,"position_name":"普陀","primary_pollutant":"颗粒物(PM2.5)","quality":"轻度污染","station_code":"1141A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":163,"area":"上海","pm2_5":129,"pm2_5_24h":124,"position_name":"十五厂","primary_pollutant":"颗粒物(PM2.5)","quality":"中度污染","station_code":"1142A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":150,"area":"上海","pm2_5":124,"pm2_5_24h":115,"position_name":"虹口","primary_pollutant":"颗粒物(PM2.5)","quality":"轻度污染","station_code":"1143A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":170,"area":"上海","pm2_5":141,"pm2_5_24h":129,"position_name":"徐汇上师大","primary_pollutant":"颗粒物(PM2.5)","quality":"中度污染","station_code":"1144A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":0,"area":"上海","pm2_5":0,"pm2_5_24h":0,"position_name":"杨浦四漂","primary_pollutant":null,"quality":null,"station_code":"1145A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":79,"area":"上海","pm2_5":0,"pm2_5_24h":0,"position_name":"青浦淀山湖","primary_pollutant":"颗粒物(PM10)","quality":"良","station_code":"1146A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":160,"area":"上海","pm2_5":103,"pm2_5_24h":122,"position_name":"静安监测站","primary_pollutant":"颗粒物(PM2.5)","quality":"中度污染","station_code":"1147A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":150,"area":"上海","pm2_5":163,"pm2_5_24h":115,"position_name":"浦东川沙","primary_pollutant":"颗粒物(PM2.5)","quality":"轻度污染","station_code":"1148A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":152,"area":"上海","pm2_5":127,"pm2_5_24h":116,"position_name":"浦东新区监测站","primary_pollutant":"颗粒物(PM2.5)","quality":"中度污染","station_code":"1149A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":0,"area":"上海","pm2_5":0,"pm2_5_24h":0,"position_name":"浦东张江","primary_pollutant":null,"quality":null,"station_code":"1150A","time_point":"2013-12-04T19:00:00Z"},
	{"aqi":146,"area":"上海","pm2_5":127,"pm2_5_24h":119,"position_name":null,"primary_pollutant":null,"quality":"轻度污染","station_code":null,"time_point":"2013-12-04T19:00:00Z"}
	]

 
**注意: 部分城市可能没有pm2.5/pm10数据；**  

### 气象局接口PM2.5数值查询接口
气象局接口目前提供PM2.5和AQI数据查询，暂没有发现PM10查询接口。该接口目前不需要申请KEY，可以直接使用。    

接口：<http://mobile.weather.com.cn/data/air/101190401.html?_=1386217771438>  
页面：<http://mobile.weather.com.cn/air/101190401.html>  

格式如下：  
 
	{"k":{"
	    k1":"?|?|?|?|?|?|?|?|?|?|?|?|?|?|?|?|?|?|?|?|?|?|?|?|?",
	    "k2":"246|239|156|?|114|114|105|105|119|139|290|300|178|178|193|217|217|217|217|217|217|217|217|217|217",
	    "k3":"296|289|206|?|148|148|137|137|155|315|340|368|228|228|243|267|267|267|267|267|267|267|267|267|267",
	    "k4":"201312051100"}
	}

解析方法如下：
	   
	$pm2d5Str = result.k.k2; 
	$pm2d5Arr = result.k.k3.split("|"); 
	$pm2d5 = $pm2d5Arr[23];
	 
	$aqiStr=result.k.k3;  
	$aqiArr=result.k.k3.split("|");  
	$aqi = $aqiArr[23];

	$publish_date=result.k.k4;   
	 


## 参考
* 参考github代码：<https://github.com/hxxy2003/Weather-weixin/blob/master/index.php>
* 一份详细解读参考：<http://g.kehou.com/t1033317914.html>
* PM参考：<http://malagis.com/recommended-air-quality-data-pm10-pm2-5.html>  


全文完（2013-12-31）


*****************************************

<img align="middle" alt="随手查查" src="/images/about/suishou_WX.jpg" width="300">

关注『**随手查查**』微信公号，支持以图搜图、天气预报、百科和火车票等信息查询。  

*****************************************


