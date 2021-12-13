
# Python爬取抖音合集视频


### 一、前期准备

### 
安装requests库。
要爬取的合集视频链接https://v.douyin.com/e4BxGEn/

### 
二、爬虫步骤


1.  首先浏览器打开合集链接：https://v.douyin.com/e4BxGEn/，然后【F12】打开开发者工具，网页上方修改设备为【Pixel2XL】，同时下方选择【Network】的tab，切换到【XHR】后，刷新一遍网页： 


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632123168852-a3702d37-3157-4eb4-a90e-91baf6dfac0f.png#clientId=ubbd5c1b3-b373-4&from=paste&height=337&id=u13abe48d&name=image.png&originHeight=673&originWidth=1546&originalType=binary&ratio=1&size=110038&status=done&style=none&taskId=uceef7bbe-2aad-4f66-b30b-c97cfe01e34&width=773)
​

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

 

2.  点击左下角红框的文件，右方【Headers】查看他的【Request URL】： 

​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632123181307-0edb47b3-dd58-4803-8d9e-7bbe747b4c2e.png#clientId=ubbd5c1b3-b373-4&from=paste&height=66&id=u788ede94&name=image.png&originHeight=132&originWidth=1240&originalType=binary&ratio=1&size=19049&status=done&style=none&taskId=u1cd7f080-a52f-45b5-826b-164bfd56e5a&width=620)


 

同时观察该url时发现参数mix_id的值与网页地址栏里的某个是一致的：
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632123191301-44bb5b05-c74a-4099-a770-09f35e2976e7.png#clientId=ubbd5c1b3-b373-4&from=paste&height=23&id=u09074c46&name=image.png&originHeight=46&originWidth=1516&originalType=binary&ratio=1&size=15033&status=done&style=none&taskId=u8819dc11-677b-4362-b62d-350a75a6365&width=758)


 

猜测mix_id表示的是该合集的id。然后选择【Preview】的tab：
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632123203497-a0733f7e-dec1-4b3f-838b-345740508b76.png#clientId=ubbd5c1b3-b373-4&from=paste&height=50&id=u64fc5f8b&name=image.png&originHeight=100&originWidth=1072&originalType=binary&ratio=1&size=13353&status=done&style=none&taskId=ud29fff71-bd9e-4c5c-9ae8-47088ef8a07&width=536)


 

大概看出来下方的数据是json格式。点开【awe_list】→【0】→【video】→【play_addr】→【url_list】→【0】，这里存放的就是合集里视频具体的链接地址（其实最重要就是找到这里，但是要怎么找到这里，我目前还是靠看其他爬取抖音的文章才找到的）：
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632123216660-65cb40c2-1589-4881-a157-57354cb55ced.png#clientId=ubbd5c1b3-b373-4&from=paste&height=92&id=u552469de&name=image.png&originHeight=184&originWidth=1481&originalType=binary&ratio=1&size=50662&status=done&style=none&taskId=u9aa5a9cb-f72d-45f1-8aa0-3c9fcae08a4&width=740.5)


 

 

把后面这个链接复制后粘贴到浏览器打开检查一遍，确定是合集的第一个视频：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632123234631-dc2cfbc6-9a44-4444-98f8-78909562a283.png#clientId=ubbd5c1b3-b373-4&from=paste&height=456&id=uc9c5c2c4&name=image.png&originHeight=911&originWidth=526&originalType=binary&ratio=1&size=813401&status=done&style=none&taskId=ubb059aca-83c7-4013-95f9-7a95f6f566b&width=263)


 

同时通过观察前面【awe_list】下的其他元素，发现刚好对应10个视频：
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632123245394-a4609b52-01a8-4907-a563-a817d22a451c.png#clientId=ubbd5c1b3-b373-4&from=paste&height=111&id=u8a7949b1&name=image.png&originHeight=221&originWidth=137&originalType=binary&ratio=1&size=5472&status=done&style=none&taskId=ud1d2b01a-e99e-4a4b-8dd8-263b39bb7e9&width=68.5)


 

故可知这里保存了10个视频。但是该合集一共有75个视频，要怎么获取剩下的那些呢？


3. 向下滑动抖音，发现动态加载出第11~20的视频出来，同时【Network】下方也多了一些文件：

​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632123267743-552116c2-8c97-427f-9872-68615748d970.png#clientId=ubbd5c1b3-b373-4&from=paste&height=142&id=u16a177c7&name=image.png&originHeight=283&originWidth=565&originalType=binary&ratio=1&size=18239&status=done&style=none&taskId=u0c47ca8a-0359-453e-8e14-8992724845a&width=282.5)


 

点开该文件，并像第2步一样点开到【url_list】下的【0】后，同样复制链接到浏览器打开，发现就是第11个视频的内容。通过观察对比发现这两个文件的区别在于最后的参数cursor：
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632123276495-61b29f57-cc98-493e-b4ce-3f9463062974.png#clientId=ubbd5c1b3-b373-4&from=paste&height=86&id=u0a036b8a&name=image.png&originHeight=171&originWidth=378&originalType=binary&ratio=1&size=8323&status=done&style=none&taskId=u802cd818-f44f-4f5e-8979-268b827eea7&width=189)


 

因此猜测就是通过这里的参数cursor来控制翻页效果的。所以下面准备开始打代码了。





### 三、编写代码


```python
import requests
 
 
headers = {
	"User-Agent": "Mozilla/5.0 (Linux; Android 5.0; SM-G900P Build/LRX21T) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.120 Mobile Safari/537.36"
}
down_path = "D:\\小丸子抖音爬虫视频\\"
total_cursor = 7  # 一共75集，偏移了7页
per_count = 10  # 每页10集
short_url = "https://v.douyin.com/e4BxGEn/"  # 合集短连接地址
main_url = requests.get(url=short_url, headers=headers).url  # 获取合集地址栏url
mix_id = main_url.split("/")[6]  # 使用切片获取合集id
prefix_url = "https://www.iesdouyin.com/web/api/mix/item/list/?mix_id="  # 【Requests URL】-前面部分
suffix_url = "&count={}".format(per_count) + "&cursor={}"  # 【Requests URL】-后面部分
 
for cursor in range(total_cursor + 1):
	page_url = prefix_url + mix_id + suffix_url.format(cursor * 10)		# 页面url
	page_json = requests.get(page_url).json()
	page_count = len(page_json["aweme_list"])  # 每个url.json()包含的视频数量
	for count in range(page_count):
		video_url = page_json["aweme_list"][count]["video"]["play_addr"]["url_list"][0]
		str_ep = "第" + str(cursor * 10 + count + 1) + "集："
		video_list.append((str_ep, video_url))
		video_name = str_ep + page_json["aweme_list"][count]["share_info"]["share_title"].replace(" ", "")  # 第几集名字
		video = requests.get(url=video_url, headers=headers).content  # 第几集的视频文件
		with open(down_path + str(video_name) + ".mp4", "wb") as f:
			f.write(video)
			f.close()
		print("第" + str(episodes) + "集下载完成，名字:" + video_name)
```
​


 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1632123298804-c68d7bf2-010c-4c90-adb5-33aaac4d58a5.png#clientId=ubbd5c1b3-b373-4&from=paste&height=284&id=ue7762512&name=image.png&originHeight=568&originWidth=918&originalType=binary&ratio=1&size=310532&status=done&style=none&taskId=uda9681ea-fb6e-4c7a-b453-32479f36682&width=459)

 

___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
