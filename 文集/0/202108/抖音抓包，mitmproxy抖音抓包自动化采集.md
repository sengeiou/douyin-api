# 抖音抓包，mitmproxy抖音抓包自动化采集



记录一下如何用python爬取app数据，本文以爬取抖音视频app为例。
编程工具：pycharm
app抓包工具：mitmproxy
app自动化工具：appium
运行环境：windows10
思路：
假设已经配置好我们所需要的工具
1、使用mitmproxy对手机app抓包获取我们想要的内容
2、利用appium自动化测试工具，驱动app模拟人的动作（滑动、点击等）
3、将1和2相结合达到自动化爬虫的效果


>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


## 一、mitmproxy/mitmdump抓包

确保已经安装好了mitmproxy，并且手机和PC处于同一个局域网下，同时也配置好了mitmproxy的CA证书，网上有很多相关的配置教程，这里我就略过了。
因为mitmproxy不支持windows系统，所以这里用的是它的组件之一mitmdump，它是mitmproxy的命令行接口，可以利用它对接我们的Python脚本，用Python实现监听后的处理。
在配置好mitmproxy之后,在控制台上输入mitmdump并在手机上打开抖音app，mitmdump会呈现手机上的所有请求，如下图
     ![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1606783086181-c999aa8c-aa4b-4940-81f5-36f975a1a345.png#align=left&display=inline&height=302&name=image.png&originHeight=604&originWidth=2654&size=187948&status=done&style=none&width=1327)
 
可以在抖音app一直往下滑，看mitmdump所展示的请求，会发现前缀分别为

```
http://v1-dy.ixigua.com/；http://v3-dy.ixigua.com/；http://v9-dy.ixigua.com/
```
这3个类型前缀的url正是我们的目标抖音视频url。
那接下来就要编写python脚本将视频下载下来，需要使用 mitmdump -s scripts.py（此处为python文件名）来执行脚本。
```
import requests
# 文件路径
path = 'D:/video/'
num = 1788
 
 
def response(flow):
    global num
    # 经测试发现视频url前缀主要是3个
    target_urls = ['http://v1-dy.ixigua.com/', 'http://v9-dy.ixigua.com/',
                   'http://v3-dy.ixigua.com/']
    for url in target_urls:
        # 过滤掉不需要的url
        if flow.request.url.startswith(url):
            # 设置视频名
            filename = path + str(num) + '.mp4'
            # 使用request获取视频url的内容
            # stream=True作用是推迟下载响应体直到访问Response.content属性
            res = requests.get(flow.request.url, stream=True)
            # 将视频写入文件夹
            with open(filename, 'ab') as f:
                f.write(res.content)
                f.flush()
                print(filename + '下载完成')
            num += 1
```
 
代码写得比较粗糙，不过基本的逻辑还是比较清晰的，这样我们就可以把抖音的视频下载下来，不过这个方法有个缺陷，就是获取视频需要人来不断地滑动抖音的下一个视频，这时候我们可以用一个强大的appium自动化测试工具来解决。
 

## 二、Appium对手机进行模拟操作

确保已经配置好appium所依赖的环境Android和SDK，网上也有许多教程，这里我就不说了。
appium的用法很简单，首先我们先打开appium，启动界面如下
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606783057792-8e12704f-1786-45da-981b-9952b3c96fae.png#align=left&display=inline&height=591&originHeight=591&originWidth=634&size=0&status=done&style=none&width=634)
点击Start Server按钮即可启动appium服务
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606783057799-86af02aa-a8ba-4f2f-95cd-d19fd0e7e7b9.png#align=left&display=inline&height=594&originHeight=594&originWidth=638&size=0&status=done&style=none&width=638)
将Android手机通过数据线与PC相连，同时打开USB调试功能，可以输入adb命令（具体可以去网上查找）测试连接情况，若出现以下结果，则说明连接成功
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606783057780-f916d9a4-5e60-4561-a8ef-7d2ac884797f.png#align=left&display=inline&height=43&originHeight=43&originWidth=755&size=0&status=done&style=none&width=755)
model是设备名，后面配置需要用到。之后点击下图箭头所指的按钮就会出现一个配置页面
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606783057762-e73fb508-61ab-405b-8008-684420409ea7.png#align=left&display=inline&height=594&originHeight=594&originWidth=638&size=0&status=done&style=none&width=638)![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606783057813-2925fcfc-b7f9-4cb2-9aec-c5e238d05022.png#align=left&display=inline&height=563&originHeight=563&originWidth=1065&size=0&status=done&style=none&width=1065)
在右下角的JSON Representation配置启动app的Desired Capabilities参数，分别是paltformName、deviceName、appPackage、appActivity。
platformName:平台名称，一般是Android或iOS.
deviceName:设备名称，手机的具体类型
appPackage:App程序包名
appActivity:入口Activity名，通常以.开头
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606783057807-254dbe2b-0245-47af-b117-66273d96d82a.png#align=left&display=inline&height=565&originHeight=565&originWidth=1067&size=0&status=done&style=none&width=1067)
platformName和deviceName比较容易获得，而appPackage和appActivity这两个可以通过以下方法获取到。
在控制台上输入   adb logcat>D:\log.log  命令，并且在手机打开抖音app，然后在D盘中打开log.log文件，查找Displayed关键字
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606783057873-d1796a55-479b-43ed-baee-857351385233.png#align=left&display=inline&height=537&originHeight=537&originWidth=1136&size=0&status=done&style=none&width=1136)
由上图可以知道Displayed后面的 com.ss.android.ugc.aweme对应的是appPackage，.main.MainActivity对应的是appActivity，最后我们的配置结果如下：
 

```
{
  "platformName": "Android",
  "deviceName": "Mi_Note_3",
  "appPackage": "com.ss.android.ugc.aweme",
  "appActivity": ".main.MainActivity"
}
```

 
再点击Start Session即可启动Android手机上的抖音app并进入到启动页面，同时PC上会弹出一个调试窗口，从这个窗口可以预览当前手机页面，还可以对手机模拟各种操作，在本文不是重点，所以略过。
 
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606783057830-c8fa7f74-699b-4219-b475-c59c920f56ac.png#align=left&display=inline&height=562&originHeight=562&originWidth=1060&size=0&status=done&style=none&width=1060)
在下面我们将使用python脚本来驱动app，直接在pycharm运行即可

```
from appium import webdriver
from time import sleep
 
 
class Action():
    def __init__(self):
        # 初始化配置，设置Desired Capabilities参数
        self.desired_caps = {
            "platformName": "Android",
            "deviceName": "Mi_Note_3",
            "appPackage": "com.ss.android.ugc.aweme",
            "appActivity": ".main.MainActivity"
        }
        # 指定Appium Server
        self.server = 'http://localhost:4723/wd/hub'
        # 新建一个Session
        self.driver = webdriver.Remote(self.server, self.desired_caps)
        # 设置滑动初始坐标和滑动距离
        self.start_x = 500
        self.start_y = 1500
        self.distance = 1300
 
    def comments(self):
        sleep(2)
        # app开启之后点击一次屏幕，确保页面的展示
        self.driver.tap([(500, 1200)], 500)
 
    def scroll(self):
        # 无限滑动
        while True:
            # 模拟滑动
            self.driver.swipe(self.start_x, self.start_y, self.start_x, 
                              self.start_y-self.distance)
            # 设置延时等待
            sleep(2)
 
    def main(self):
        self.comments()
        self.scroll()
 
 
if __name__ == '__main__':
 
    action = Action()
    action.main()
```

 
下面是爬虫的过程。ps:偶尔会爬取到重复的视频
![](https://cdn.nlark.com/yuque/0/2020/gif/97322/1606783057837-a42d5f32-15c1-4a4d-8ee5-69ae700b3580.gif#align=left&display=inline&height=500&originHeight=500&originWidth=284&size=0&status=done&style=none&width=284)




![20201201084141u8221-2.gif](https://cdn.nlark.com/yuque/0/2020/gif/97322/1606783430346-24bbc33f-b15f-4a16-9a74-85aadd144cf3.gif#align=left&display=inline&height=428&name=20201201084141u8221-2.gif&originHeight=428&originWidth=1121&size=2688898&status=done&style=none&width=1121)





___________________
免责申明：此内容仅供学习交流使用，若侵犯贵公司的权益，联系作者删除
