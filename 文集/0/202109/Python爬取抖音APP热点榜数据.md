
# Python爬取抖音APP热点榜数据


**今天给大家分享一篇简单的安卓app数据分析及抓取方法。以抖音 为例，我们想要抓取抖音的热点榜数据。**
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632124414103-515f173d-31e2-4bad-86a6-c82ba1d6fe2f.png#clientId=ufefb85c0-777f-4&from=paste&id=u41ad1b08&name=image.png&originHeight=1138&originWidth=640&originalType=url&ratio=1&size=517294&status=done&style=none&taskId=uc6fa72d3-aef8-4f19-a941-b67bbabf601)
​

 

要知道，这个数据是没有网页版的，只能从手机端下手。
首先我们要安装charles抓包APP数据，它是一款收费的抓包修改工具，易上手，数据请求容易控制，修改简单，抓取数据的开始暂停方便等优势，网上也有汉化版，下载地址为http://www.zdfans.com/html/42074.html，一路默认安装就ok了。
安装完成后要设置代理，依次点击代理——代理设置。
​
>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632124413860-12b0df20-76bf-4321-aa32-cdfecda81c5b.png#clientId=ufefb85c0-777f-4&from=paste&id=uf93f135e&name=image.png&originHeight=518&originWidth=561&originalType=url&ratio=1&size=80657&status=done&style=none&taskId=u9918cd42-6306-499d-9f09-1eb3e7e76d8)
​

 

然后在手机端设置代理，如下图所示：
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632124413918-651ade83-330d-490c-ace6-284e51dc5049.png#clientId=ufefb85c0-777f-4&from=paste&id=udf6843c8&name=image.png&originHeight=678&originWidth=489&originalType=url&ratio=1&size=54516&status=done&style=none&taskId=ud610c98d-0104-47e6-baf2-56ad6f149d1)
​

 

在保证手机和电脑在同一局域网的情况下，代理服务器主机名设为电脑的ip地址，端口设为8888。
最后在电脑端和手机端分别安装证书。
电脑端安装方法：依次点击帮助——ssl代理——安装charles root证书 ，按下图进行安装。
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632124413969-3ce5fcf5-4978-4bed-94a2-5a26d6057d26.png#clientId=ufefb85c0-777f-4&from=paste&id=ub6dae9a2&name=image.png&originHeight=424&originWidth=506&originalType=url&ratio=1&size=152450&status=done&style=none&taskId=u88be07bc-0720-4df4-84cf-17d5d17ce38)
​

 

手机端安装方式：帮助——ssl代理——在移动设备或远程浏览器上安装charles root证书。
再在模拟器浏览器中输入chls.pro/ssl，会自动下载手机端证书
最后再手机端依次点击设置——安全——从SD卡安装。
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632124413923-c643d942-b8a4-4bc5-b95e-dbb38a801d4b.png#clientId=ufefb85c0-777f-4&from=paste&id=u6dc06a53&name=image.png&originHeight=680&originWidth=558&originalType=url&ratio=1&size=50166&status=done&style=none&taskId=uc84be9e5-f75d-4dca-b67d-a17ec691c45)
​

 

为证书命名，点击确认就安装成功了。
打开charles，然后打开抖音app的热点榜界面，在charles很容易就找到了数据接口，一次就返回了50条数据，如下图所示。
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632124414921-fc24199d-61d1-4d08-b616-ca914d4c0d0d.png#clientId=ufefb85c0-777f-4&from=paste&id=u4019a456&name=image.png&originHeight=356&originWidth=640&originalType=url&ratio=1&size=192645&status=done&style=none&taskId=ud837df09-7d6e-466e-b3c4-bf8d4e3a832)
​

 

它的url信息如下图所示。
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632124414921-89f98ac5-b68c-4f5f-aa60-95a8c231e2e2.png#clientId=ufefb85c0-777f-4&from=paste&id=u2d841e7f&name=image.png&originHeight=410&originWidth=463&originalType=url&ratio=1&size=44253&status=done&style=none&taskId=uddd7660f-f2ae-428e-811e-a21fa9a2b5d)
​

 

此接口只能返回这一时刻的热点数据，要想返回新的数据，就要变换参数信息，但是App端的数据接口参数都比较复杂，这里我们不再深入分析。
为了解决这一问题，我们可以用appium定时模拟操控手机，然后用mitmproxy把数据拦截下来（关于appium、mitmproxy的简介与安装网上有很多教程，这里不再赘述）
Appium脚本如下图所示：
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632124414972-5e216441-cbd7-4f14-ba54-eca73c398417.png#clientId=ufefb85c0-777f-4&from=paste&id=u08350290&name=image.png&originHeight=510&originWidth=640&originalType=url&ratio=1&size=155558&status=done&style=none&taskId=ufdf2a828-3972-499f-aef2-a692afda6b8)
​

 

这个自动化测试脚本比较简单，主要是重复获取热点最新信息。
Mitmproxy脚本如图：
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632124415050-ef38b64e-211d-4009-907d-2deafe09d76b.png#clientId=ufefb85c0-777f-4&from=paste&id=u7eec52a6&name=image.png&originHeight=309&originWidth=640&originalType=url&ratio=1&size=103425&status=done&style=none&taskId=u2d1850f3-878c-4e5c-a85b-b496dc20d17)
​

 

有4点需要注意的地方：

1. 用mitmproxy抓包前，先把手机代理ip端口设置为8080，设置方法同上；
1. 要想在此脚本运行外置函数，必须加上前两行，要不然会出错；
1. 脚本中if url in flow.request.url为数据流判断条件，如果url在该数据流的url请求数据中，则判断该数据为抖音app热点数据；
1. 最后在脚本所在路径运行以下程序：

​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632124415409-3f87cf9c-f84b-4202-ba77-63a12cb2e579.png#clientId=ufefb85c0-777f-4&from=paste&id=ud54929a1&name=image.png&originHeight=80&originWidth=455&originalType=url&ratio=1&size=4786&status=done&style=none&taskId=u9a24a9fa-6ea0-46ee-9ea4-9d6339a4f04)
​

 

最后再运行appium自动化测试脚本，就大功告成了。

___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
