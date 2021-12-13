
# 抖音爬取粉丝用户列表_Python手机App数据抓取实战

## 环境准备

- fiddler
- appium
- mitmproxy(mitmdump)
- python3.6
- 自带root的安卓虚拟机
- Android SDK

​

安卓模拟器需要安装xposed框架并安装JustTrustMe组件，因为抖音会有ssl验证，会导致我们在将数据发送到我们的抓包工具的时候无法联网，所以需要安装这个组件来关闭ssl验证
​

mitmproxy、AndroidSDK需要加入环境变量，这步就不再叙述
​


---



需要数据采集接口SDK请[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)

---




## 项目准备
首先我们需要给虚拟机安装fiddler、mitmproxy的证书
**1)fiddler 主要设置如下**



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466420760-43f0265f-0e27-4e77-8b11-49c23f5f12d3.png#clientId=u5deefe71-fe2d-4&from=paste&height=230&id=ud6815f88&name=image.png&originHeight=460&originWidth=678&originalType=binary&ratio=1&size=81113&status=done&style=none&taskId=u4963ad08-c6ff-4f41-a39c-18c2c4bfa4d&width=339)

 




 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466440037-9985f982-1792-47fe-9fc9-28dcf90204d4.png#clientId=u5deefe71-fe2d-4&from=paste&height=230&id=u1587be1b&name=image.png&originHeight=460&originWidth=678&originalType=binary&ratio=1&size=87873&status=done&style=none&taskId=ud9bf7adc-ea0d-4881-9edc-a10d3dd290d&width=339)

 




 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466452919-48b68c83-c55b-4f04-82fe-cfb16638f623.png#clientId=u5deefe71-fe2d-4&from=paste&height=230&id=u50281555&name=image.png&originHeight=460&originWidth=678&originalType=binary&ratio=1&size=107374&status=done&style=none&taskId=u4cfb4781-cdb6-41df-a000-008494f9663&width=339)

 


端口设置可以随意更改，本文设置为8889
电脑主机打开命令行 输入ipconfig查看本机IP



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466464731-4f2bf2d1-ae21-43a9-b8b3-d94620b91d90.png#clientId=u5deefe71-fe2d-4&from=paste&height=87&id=u87a952ac&name=image.png&originHeight=173&originWidth=716&originalType=binary&ratio=1&size=61785&status=done&style=none&taskId=u72258715-7afe-49c2-9ac2-c632b095440&width=358)

 


设置模拟器的代理



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466479502-5d036c90-13cd-4a3f-8293-517b6c3c9398.png#clientId=u5deefe71-fe2d-4&from=paste&height=417&id=uf7d9fac0&name=image.png&originHeight=834&originWidth=454&originalType=binary&ratio=1&size=73019&status=done&style=none&taskId=u3a070bd9-8e24-4292-b82c-cfa31a4f349&width=227)

 



接下来打开浏览器，输入 ip:prot 如本机为 117.90.211.134:8889 下载安装fiddler证书 如下步骤

 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466502554-fb7388c0-983a-4b2c-9080-22b7988ee962.png#clientId=u5deefe71-fe2d-4&from=paste&height=417&id=u0b98c454&name=image.png&originHeight=834&originWidth=454&originalType=binary&ratio=1&size=55923&status=done&style=none&taskId=u2d8bfae3-f98f-4c4c-bd65-2f869636b20&width=227)

 


接下来我们就可以使用fiddler正确的抓取手机数据包了
​

**2）mitmproxy证书**
我们在Windows系统上常用的是mitmproxy的mitmdump 打开cmd输入mitmdump -p 端口号 就可以启动服务



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466520555-aad13534-71ad-4149-b528-96acebc62ab6.png#clientId=u5deefe71-fe2d-4&from=paste&height=173&id=u82d55c63&name=image.png&originHeight=346&originWidth=1040&originalType=binary&ratio=1&size=22699&status=done&style=none&taskId=u58149a6f-4e5e-41d2-acde-65e10c1cae1&width=520)

 


为了方便我们将端口号也设置为8889 但是开启这个的时候需要先关闭fiddler 不然会端口冲突，然后打开模拟器的浏览器看看能否接受数据



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466538945-ff8b6321-5c82-4c61-be75-1668de4221ab.png#clientId=u5deefe71-fe2d-4&from=paste&height=296&id=u236dbff7&name=image.png&originHeight=591&originWidth=1183&originalType=binary&ratio=1&size=488052&status=done&style=none&taskId=u9ae1dd1b-4b08-407a-ae16-ac12b9248ef&width=591.5)

 


我们发现出问题了，发现现在直接提示空的响应而不是证书的问题，看到mitmdump报的错误是 killed by block_global 是什么原因呢？
这是mitmdump的自我保护措施，它防止全球网络的连接，意思就是如果是本地局域网它不会阻拦，那该怎么办呢？
有两种解决办法：第一种，将模拟器的网络连接设置为桥接模式，这样就不会出现问题；
第二种办法是启动的时候加入参数 mitmdump -p 8889 --set block_global=false 如下图



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466553188-d2fe2553-065f-463d-b0e9-25ca28d1c6ab.png#clientId=u5deefe71-fe2d-4&from=paste&height=338&id=u29be8d1b&name=image.png&originHeight=676&originWidth=1151&originalType=binary&ratio=1&size=180458&status=done&style=none&taskId=ua3ac85c2-221a-4e5a-8c48-3a8090b6bc3&width=575.5)

 


接下来 安装mitmproxy的证书 浏览器输入网址 mitm.it 然后安装证书



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466567066-1e132897-96c7-48dd-8183-097a847a41d7.png#clientId=u5deefe71-fe2d-4&from=paste&height=417&id=ue1ea4b8e&name=image.png&originHeight=834&originWidth=454&originalType=binary&ratio=1&size=45875&status=done&style=none&taskId=u3678460b-affe-4370-9bbe-9b75bbd30eb&width=227)

 


现在再打开网页就不会出现证书的提醒了
这时候我们需要用的主要两个抓包工具的证书就安装完成了，需要注意的是我们每次测试都可能需要重新安装证书，因为我们的主机IP可能会变

## App分析
首先呢我们先确认模拟器中的xposed框架的JustTrustMe组件是否开启 不然我们抖音App不能正常访问网络



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466580544-2a715022-f64f-4982-b945-a4ffb699ae79.png#clientId=u5deefe71-fe2d-4&from=paste&height=218&id=u3cbf4df0&name=image.png&originHeight=435&originWidth=492&originalType=binary&ratio=1&size=29419&status=done&style=none&taskId=u8d6d9d9d-fb93-4fc7-b5c4-de14b72a72b&width=246)

 


注意以下步骤只能在网络桥接模式下使用（或者使用真机与电脑在同一个网络） 否则fiddler无法抓取到所有的包
首先使用fiddler分析，打开fiddler 并打开抖音进行操作 本项目是爬取cxk坤坤的粉丝数据，所以我们先进入他的抖音主页点进粉丝列表



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466593333-0a1b1e0f-cbec-4876-a4b5-c8aafc2f7131.png#clientId=u5deefe71-fe2d-4&from=paste&height=418&id=ufdec5c60&name=image.png&originHeight=835&originWidth=1441&originalType=binary&ratio=1&size=412316&status=done&style=none&taskId=u6163f30b-0b6b-44e5-84ca-34bd29f1069&width=720.5)

 


粉丝好多，我们接下来向下滑动，看看当向下滑动刷新出更多粉丝的时候 fiddler会抓取到什么数据



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466607812-f2b4e053-4c1d-4390-8c25-fecd25c48fcc.png#clientId=u5deefe71-fe2d-4&from=paste&height=408&id=u5a4e9d3f&name=image.png&originHeight=816&originWidth=1426&originalType=binary&ratio=1&size=444891&status=done&style=none&taskId=u6896890c-95c4-4b5a-986a-a1806c41608&width=713)

 


会发现每次向下滑动的时候都会有一个含有 aweme/v1/user/follower/list/ 的网址，我们怀疑这就是粉丝数据的接口 我们把这个网址返回来的数据放入 json.cn 看看



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466634593-656ca0b4-286e-4474-9914-d3dbb1ba44e8.png#clientId=u5deefe71-fe2d-4&from=paste&height=388&id=u264e63c0&name=image.png&originHeight=776&originWidth=1528&originalType=binary&ratio=1&size=120158&status=done&style=none&taskId=u1c7884e4-26a3-4fd8-8e75-29a0ef01984&width=764)

 


还真的就是粉丝数据，会发现每次滑动更新的是二十个粉丝，所以我们就找对了方向，下面我们来分析这个请求
```python
GET https://aweme-hl.snssdk.com/aweme/v1/user/follower/list/?user_id=103313639528&sec_user_id=MS4wLjABAAAAxj2Cuu75g3I2pGOs7jtw5XN6WMiCKbA-jfIjlONRRvM&max_time=1570336550&count=20&offset=0&source_type=1&address_book_access=1&gps_access=1&openudid=3ca06768d1f58615&version_name=8.1.1&ts=1570336895&device_type=OPPO%20R11&ssmix=a&iid=87664447665&app_type=normal&os_api=19&mcc_mnc=46007&device_id=68799320259&resolution=720*1280&device_brand=OPPO&aid=1128&manifest_version_code=811&app_name=aweme&_rticket=1570336895512&os_version=4.4.2&device_platform=android&version_code=811&update_version_code=8112&ac=wifi&dpi=240&uuid=866174010601603&language=zh&channel=tengxun_new HTTP/1.1
```
我们会发现这个请求的url带有很多很多的参数，没错，这是抖音自身的加密方法，要破解这个可得好一会了，所以我们无法使用requests直接构造请求来获得数据了，那我们该如何获取数据呢？
没错就是使用mitmdump，mitmdump有个最大的好处就是可以与python文件交互，我们可以直接使用python写好命令，使用mitmdump抓包，我们手动的滑动就可以解析出数据了
编写douyin_mitmdump.py文件 代码如下：


```python
import json

#函数名必须这样写 这是mitmdump规则
def response(flow):
    #下面这个网址是通过fiddler获取到的 但是有些数据我们无法解密，所以需要用mitmdump捕获数据包然后做分析
    if 'aweme-hl.snssdk.com/aweme/v1/user/follower/list' in flow.request.url:
        for user in json.loads(flow.response.text)['followers']:
            user_info={}
            user_info['nickname'] = user['nickname']
            user_info['share_id'] = user['uid']
            user_info['douyin_id'] = user['short_id']
            #有的用户修改了抖音号
            if user_info['douyin_id'] == '0':
                user_info['douyin_id'] = user['unique_id']
            print(user_info)

```


现在打开cmd并切换到项目目录下 执行命令 mitmdump -p 8889 -s douyin_mitmdump.py



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466676202-48dcbb88-365c-406e-a3a0-6ff0e519b889.png#clientId=u5deefe71-fe2d-4&from=paste&height=152&id=ue6ce728c&name=image.png&originHeight=304&originWidth=1055&originalType=binary&ratio=1&size=69980&status=done&style=none&taskId=u13267444-7752-4c75-b073-3efb7ea4d65&width=527.5)

 


接下来手动滑动界面看看会不会解析数据



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466690023-2762e25a-35d7-4194-931e-358649ae2fb3.png#clientId=u5deefe71-fe2d-4&from=paste&height=397&id=u16db6f09&name=image.png&originHeight=794&originWidth=1445&originalType=binary&ratio=1&size=371924&status=done&style=none&taskId=ud19eeb83-9136-42b8-8e65-58192048342&width=722.5)

 


好的 现在我们已经成功的分析出来了粉丝数据，但是我们总不能一直鼠标滑动吧？所以我们现在需要使用Appium进行自动化测试模拟滑动

## Appium自动化测试模拟滑动
**配置信息**
Appium是一个开源测试自动化框架，可用于原生，混合和移动Web应用程序测试。 它使用WebDriver协议驱动iOS，Android和Windows应用程序。
比如本文我们就使用appium来实现从点开程序到模拟滑动的全部操作。
首先我们需要在电脑上安装Appium



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466708735-c56ca554-326d-4969-abdd-af83ef2cb9f2.png#clientId=u5deefe71-fe2d-4&from=paste&height=372&id=u97731b18&name=image.png&originHeight=743&originWidth=797&originalType=binary&ratio=1&size=44948&status=done&style=none&taskId=u4c691533-9b4c-4839-9c5f-a8c7694e462&width=398.5)

 


这个相当于appium服务端，我们在执行自动化测试的时候需要先在电脑上打开服务端，然后我们使用程序连接虚拟机或真机执行脚本进行自动化测试 点击start server打开服务端



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466723728-9276e2ea-90bb-43bb-959b-a1ba1a6132b1.png#clientId=u5deefe71-fe2d-4&from=paste&height=178&id=u143e51b6&name=image.png&originHeight=355&originWidth=806&originalType=binary&ratio=1&size=24079&status=done&style=none&taskId=u206c8c07-7e4a-4766-b28c-26f63fba1a5&width=403)

 


我们首先使用appium 自带的测试程序来试一下如何操作 点击右上角的放大镜符号
进入配置选项界面 开始填写选项信息



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466736592-39455835-a312-4b4a-9fec-485fc3d6eade.png#clientId=u5deefe71-fe2d-4&from=paste&height=481&id=u83a4175d&name=image.png&originHeight=962&originWidth=1394&originalType=binary&ratio=1&size=104621&status=done&style=none&taskId=u7c1291e0-0f55-40aa-829d-46866fa4272&width=697)

 


下面我来解释每个参数都是如何得到的
1）platformName 这是平台名称 我们填写 Andriod 相信不用过多解释
2）platformVersion 这是问平台版本信息，根据各个手机不同自主填写，本文使用的是 Andriod4.4.2
3）deviceName 这个是设备的名称，我们如何获取呢？
这个时候就用到了我们AndroidSDK中的adb工具了 adb是用来连接电脑与手机的工具 我们把手机进入开发者选项并打开允许USB调试，然后打开命令行输入命令 adb devices看看有没有输出



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466754136-7cb92be1-6c53-4006-9d73-18c295ccc325.png#clientId=u5deefe71-fe2d-4&from=paste&height=280&id=ud3a4a69f&name=image.png&originHeight=559&originWidth=1151&originalType=binary&ratio=1&size=154435&status=done&style=none&taskId=u2965246e-46a3-485e-9171-174dda4e83b&width=575.5)

 


返回的127.0.0.1:52001就是设备名称 这个是模拟器的名称，使用真机会不同 （如果没有返回就关闭开发者模式重新打开USB调试多试几次）
4）appPackage、appActivity这俩个参数非常重要，它指定了我们自动化测试的app，这两个参数获取有点麻烦，下面详解如何获取
首先手机打开App，本文就是抖音了，然后电脑命令行输入 adb shell 进入交互界面 然后输入命令 dumpsys activity | grep mFocusedActivity



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466316810-185a270a-0fe3-49f0-8fe0-6c1f748d6545.png#clientId=u5deefe71-fe2d-4&from=paste&id=ufd8914b5&name=image.png&originHeight=162&originWidth=1045&originalType=url&ratio=1&size=39368&status=done&style=none&taskId=u806da18b-5696-4331-9331-6c3e25d01c7)

 


第一个就是包名，第二个就是activity名 我们记下来一会编写进去（activity名称前要跟着包名）
即 包名com.ss.android.ugc.aweme activity名com.ss.android.ugc.aweme.main.MainActivity
5）noReset unicodekeyboard resetkeyboard解释会在一会的程序中
然后点击右下方保存配置信息并start session 如果点击start session发现手机自动打开抖音时，就说明我们的配置信息写对了，就可以开始使用了
这些配置信息一会要在我们的python脚本中使用，所以一定要填写正确

## 内容分析
其实app自动化测试跟网页爬虫很相似，首先分析我们该怎么做
点开抖音这一步已经不用我们去做 然后我们需要依次点左上角放大镜按钮、点击搜索框输入抖音号、点击搜索、点击用户、点击进入主页、点击粉丝、向上滑动



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466807382-dfa941a2-6292-437d-a825-70279ea74f9e.png#clientId=u5deefe71-fe2d-4&from=paste&height=239&id=uc668c27d&name=image.png&originHeight=478&originWidth=797&originalType=binary&ratio=1&size=690916&status=done&style=none&taskId=u4f7db4f9-5188-428e-9567-f8917d6ad2d&width=398.5)

 




 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466820726-1b5728d7-280e-4e23-bc72-7d5058b57d4f.png#clientId=u5deefe71-fe2d-4&from=paste&height=247&id=ud7e0a893&name=image.png&originHeight=493&originWidth=804&originalType=binary&ratio=1&size=562339&status=done&style=none&taskId=ub4986095-1765-49f9-810a-80bbdfc7f07&width=402)

 


我们该如何定位按钮以及输入信息呢？
这又得使用AndroidSDK中的工具了 这次使用 AndroidSDK\tools\monitor.bat 中的monitor 可能有的小伙伴会问 为什么不使用可以查看xpath的魔改版的uiautomatorviewer呢?这是因为我在测试的时候发现现在抖音加入了某种时钟，我们都知道uiautomatorviewer无法获取动态页面的数据，而我发现monitor有的界面却可以，所以使用monitor，下面开始



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466832358-abdd496b-28b0-4f93-8106-c61d7d155fa1.png#clientId=u5deefe71-fe2d-4&from=paste&height=403&id=ub2cf074e&name=image.png&originHeight=806&originWidth=1634&originalType=binary&ratio=1&size=653483&status=done&style=none&taskId=u5899b280-991d-40ec-8201-0c1d38bad96&width=817)

 


首先用鼠标点击左边画蓝色圈的地方获取当前手机界面数据，然后鼠标点击我们需要的控件，之后右边出现的就是我们需要的信息，我们可以通过resource-id查找指定数据
现在先来编写douyin_appium.py文件测试下能否自动打开抖音并点击左上方放大镜按钮


```python
from appium import webdriver
#WebDriverWait用来加入时间判断，有时候控件元素需要过一段时间才会出现
from selenium.webdriver.support.ui import WebDriverWait
import time

#配置信息
option={
    "platformName": "Android",
    "platformVersion": "4.4.2",
    "deviceName": "127.0.0.1:52001",
    #自动化测试包名
    "appPackage": "com.ss.android.ugc.aweme",
    #自动化测试Activity
    "appActivity": "com.ss.android.ugc.aweme.main.MainActivity",
    #再次启动不需要再次安装
    "noReset": True,
    #unicode键盘 我们可以输入中文
    "unicodekeyboard": True,
    #操作之后还原回原先的输入法
    "resetkeyboard":True
}

#其中的4723就是appium服务启动时的端口号
driver = webdriver.Remote("http://localhost:4723/wd/hub",option)

#放大镜按钮
try:
    #使用resource-id查找按钮
    if WebDriverWait(driver,5).until(lambda x:x.find_element_by_id('com.ss.android.ugc.aweme:id/b3o')):
        #点击按钮
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/b3o').click()
except:
    pass

```
​

运行之前首先要开启 appium 服务端，即start server
然后运行python文件



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466864468-50c634a2-6d40-4ce8-8c13-98f8e0fc8fed.png#clientId=u5deefe71-fe2d-4&from=paste&height=451&id=u0efbc151&name=image.png&originHeight=901&originWidth=1637&originalType=binary&ratio=1&size=719350&status=done&style=none&taskId=u007677e5-eac7-413c-ba09-eb114aba6ac&width=818.5)

 

---

需要数据采集接口SDK请[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)

---

好的我们发现已经可以自动的点击放大镜按钮了，那么接下来只需要继续编写文件即可以完成自动化操作了 代码如下


```python
from appium import webdriver
#WebDriverWait用来加入时间判断，有时候控件元素需要过一段时间才会出现
from selenium.webdriver.support.ui import WebDriverWait
import time

#配置信息
option={
    "platformName": "Android",
    "platformVersion": "4.4.2",
    "deviceName": "127.0.0.1:52001",
    #自动化测试包名
    "appPackage": "com.ss.android.ugc.aweme",
    #自动化测试Activity
    "appActivity": "com.ss.android.ugc.aweme.main.MainActivity",
    #再次启动不需要再次安装
    "noReset": True,
    #unicode键盘 我们可以输入中文
    "unicodekeyboard": True,
    #操作之后还原回原先的输入法
    "resetkeyboard":True
}

#其中的4723就是appium服务启动时的端口号
driver = webdriver.Remote("http://localhost:4723/wd/hub",option)

#放大镜按钮
try:
    #使用resource-id查找按钮
    if WebDriverWait(driver,5).until(lambda x:x.find_element_by_id('com.ss.android.ugc.aweme:id/b3o')):
        #点击按钮
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/b3o').click()
except:
    pass

#得到窗口大小
def get_size():
    x=driver.get_window_size()['width']
    y=driver.get_window_size()['height']
    return (x,y)

#搜索框
try:
    # 定位搜索框
    if WebDriverWait(driver,3).until(lambda x:x.find_element_by_id('com.ss.android.ugc.aweme:id/ad1')):
        #点击搜索框
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/ad1').click()
        #输入抖音号并点解搜索
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/ad1').send_keys("1307311292")
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/dy8').click()
        #点击用户 注意写法 这个控件由于无法获取resource_id与xpath的值 所以只能通过text来查找
        driver.find_element_by_android_uiautomator("text(\"用户\")").click()
except:
    pass

#点进去具体界面
try:
    if WebDriverWait(driver,5).until(lambda x:x.find_element_by_id('com.ss.android.ugc.aweme:id/bck')):
        #进入用户信息界面
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/bck').click()
        time.sleep(2)
        #点击粉丝数
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/akf').click()
except:
    pass

#得到屏幕尺寸
size = get_size()
#定义滑动
x1 = int(size[0]*0.5)
x2 = int(size[0]*0.7)
y1 = int(size[1]*0.9)
y2 = int(size[1]*0.2)

while(True):
    time.sleep(0.5)
    #模拟滑动
    driver.swipe(x1, y1, x2, y2)

```




---

___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
