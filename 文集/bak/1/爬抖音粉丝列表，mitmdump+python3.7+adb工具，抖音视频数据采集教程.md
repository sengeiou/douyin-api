
## 爬抖音粉丝列表，mitmdump+python3.7+adb工具，抖音视频数据采集教程

目标是实现 抖音粉丝列表的粉丝信息


使用的工具包含：mitmdump+python3.7+adb工具


工具的安装与环境配置：自行百度或去各自官网下载
​



>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 




### 

### 【目标一】通过adb工具实现滑动粉丝列表，达到请求数据的目的。


代码中使用多进程和多设备管理，实现两个手机同时进行滑动


```http
import time
import os
from threading import Thread
from multiprocessing import Process
 
def page(b):
	c = 'adb -s ' +b.replace("	device","").strip()+' shell input swipe 300 1100 300 200'
	print(" ---------> ", c)
	while c:
		os.system(c)
		
 
def get_view():
	c_view = os.popen('adb shell "dumpsys window | grep mCurrentFocus"').read()
	if "profile.ui.UserProfileActivity" in c_view:
		return False
	else:
		return True
 
 
if __name__ == '__main__':
	a = os.popen("adb devices")
	c = a.readlines()
	
	ts = []
 
	for b in c[1:-1]:
		t = Process(target=page, args=(b,))
		ts.append(t)
 
	for myt in ts:
		myt.start()
 
	for myt in ts:
		myt.join()
 
 
```







### 【目标二】通过mitmdump+python实现抓包（如果希望存到自己的数据库，这里不做详细介绍）


代码中已实现数据入库（mongodb）


```http
import json
import pymongo
import os
 
 
def response(flow):
	url = 'aweme/v1/user/follower/list'
	if url in flow.request.url:
		for user in json.loads(flow.response.text)['followers']:
			
			douyin_info = {}
			douyin_info['uid'] = user['uid']
			douyin_info['unique_id'] = user['unique_id']
			douyin_info['short_id'] = user['short_id']
			douyin_info['nickname'] = user['nickname']
			# save(data=douyin_info)
			try:
				save(data=douyin_info)
			except Exception as e:
				print(e)
				pass
	elif "" in flow.request.url:
		print(flow.request.url)
 
 
def save(data):
    DATABASE_IP = '127.0.0.1'
    DATABASE_PORT = 27017
    DATABASE_NAME = '抖音'
    client = pymongo.MongoClient(DATABASE_IP, DATABASE_PORT)
    db = client[DATABASE_NAME]
    collection = db['粉丝列表']  # 准备插入数据
    collection.insert_one(data)
	
 
 
 
```



### 【输出成果】



![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628727684796-994b0a55-524c-4ffb-b9d5-40b9eff129c3.png#clientId=ue81bdebe-ea66-4&from=paste&height=172&id=u96f233e4&name=image.png&originHeight=344&originWidth=594&originalType=binary&ratio=1&size=48177&status=done&style=none&taskId=u69ed93a9-b71b-4d07-ba56-0e5834a5954&width=297)
[
](https://blog.csdn.net/xinxianren007/article/details/104666950)



