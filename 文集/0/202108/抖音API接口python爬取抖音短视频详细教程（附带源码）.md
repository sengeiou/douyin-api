​
## 抖音API接口python爬取抖音短视频详细教程（附带源码）

## 1. 环境的准备
夜神模拟器（Android 5.1.1）
filder
mitmproxy
python3.7.3
jdk1.8.0_251
Android Studio（Android SDK 5.1）
xposed（Android 5.1）
JustTrustMe
这里先说明一下，抖音有ssl验证，夜神模拟器安装xposed和JustTrustMe之后，才能正常抓包，不然打开抖音是没有网的。
​

> **短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)

​


### 2. 环境配置
除了python3.7.3和mitmproxy，其余的资源都在百度网盘中，自取，地址：[https://pan.baidu.com/s/1ZtvZIQvQ8A6rp02HtAOudQ](https://pan.baidu.com/s/1ZtvZIQvQ8A6rp02HtAOudQ)，提取码：4mu1。
​

2.1 filder配置
安装好filder后，打开filder，左上角Tools -> Options，设置如下：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993258599-5ec79921-6f2e-4700-95c7-b01e332a1e8a.png#clientId=u5e3c67b1-925f-4&from=paste&height=188&id=ufe5c922e&name=image.png&originHeight=376&originWidth=558&originalType=binary&ratio=1&size=22933&status=done&style=none&taskId=u8a2addf3-c3bc-4261-ad0c-ec075aaac60&width=279)

 

 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993286937-88b0e579-9a78-4071-9096-ad8f23747bae.png#clientId=u5e3c67b1-925f-4&from=paste&height=188&id=u975eeb1c&name=image.png&originHeight=376&originWidth=558&originalType=binary&ratio=1&size=24600&status=done&style=none&taskId=ufffd65d6-2417-4b44-9df1-4988c6c912c&width=279)

 
​


点击Actions，选择Trust Root Certificate，然后全部选是，安装window端的证书
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993303135-17f4fee6-3b12-47bf-aed1-cedc2e341218.png#clientId=u5e3c67b1-925f-4&from=paste&height=188&id=u636f6fa2&name=image.png&originHeight=376&originWidth=558&originalType=binary&ratio=1&size=29040&status=done&style=none&taskId=ue314ce64-4a76-4d99-b51a-ef7f0f5116b&width=279)

 
​


到这一步filder，PC端的算是配好了，然后关闭域防火墙，获取PC端的ip地址，方法如下：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993313580-3f0f4548-4cd9-4ed7-8828-689d973b1ce5.png#clientId=u5e3c67b1-925f-4&from=paste&height=136&id=u56a250ca&name=image.png&originHeight=272&originWidth=255&originalType=binary&ratio=1&size=12510&status=done&style=none&taskId=u2cb7f404-ec1b-4e60-a986-9ba987e0048&width=127.5)

 
​


然后打开夜神模拟器，进入wifi，配置代理：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993326622-c8ababef-f87e-4b9f-9bca-9fab249bd30f.png#clientId=u5e3c67b1-925f-4&from=paste&height=510&id=u6774e2fd&name=image.png&originHeight=1020&originWidth=558&originalType=binary&ratio=1&size=69188&status=done&style=none&taskId=ub73839d0-b908-47e3-9c50-5759c909270&width=279)

 
​


然后打开网页进入192.168.1.19:8888，下载证书，
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993339045-30f5019d-b4f8-4ebe-92fe-3821f9ddf964.png#clientId=u5e3c67b1-925f-4&from=paste&height=206&id=u15cc9535&name=image.png&originHeight=411&originWidth=558&originalType=binary&ratio=1&size=54366&status=done&style=none&taskId=uda809f0f-cc63-4749-9074-5cb642bff25&width=279)

 
​


下载完成了，进入设置 -> 安全 -> 从SD卡安装，安装刚刚下载的证书，随便取名，
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993351171-e8fe25d1-1a44-4a8b-8df0-076e78e7d5c0.png#clientId=u5e3c67b1-925f-4&from=paste&height=378&id=u85670781&name=image.png&originHeight=755&originWidth=558&originalType=binary&ratio=1&size=56026&status=done&style=none&taskId=u7b602760-2d99-4ab3-a573-1c0874cd8b4&width=279)

 
​


到处，filder就可以抓到夜神模拟器上的数据包了，而要抓抖音的包得安装xposed和JustTrustMe。
​

2.2 xposed和JustTrustMe的配置
在网盘中下好xposed和JustTrustMe的apk文件后，直接点夜神右侧的安装APK，找到这两个框架的apk文件，点击安装，这里有个先后顺序，先安装xposed，安装后夜神桌面有一个xposed install，打开，然后下载xposed，
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993365790-dd7d672d-4e83-40cd-b0ed-1ff433248904.png#clientId=u5e3c67b1-925f-4&from=paste&height=391&id=u0b07e426&name=image.png&originHeight=781&originWidth=558&originalType=binary&ratio=1&size=57443&status=done&style=none&taskId=uc96e82a8-38b3-4482-97ad-b9776793449&width=279)

 
​


下载后会显示Xposed框架89版已激活，然后安装JustTrustMe的apk，安装好后，在xposed install里面勾选JustTrustMe，
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993378382-d4f1150a-73ba-475f-8804-0fd4d3707f5f.png#clientId=u5e3c67b1-925f-4&from=paste&height=128&id=u8d691a0b&name=image.png&originHeight=255&originWidth=558&originalType=binary&ratio=1&size=25441&status=done&style=none&taskId=uafab3080-b9fd-42a5-a216-714b62c080a&width=279)

 
​


到这里就安装完成了，然后下载最新版的抖音，就可以用filder抓包了：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993393194-842d7bec-9a59-4a21-b7af-a134e1f05d82.png#clientId=u5e3c67b1-925f-4&from=paste&height=293&id=ub7cc73d5&name=image.png&originHeight=585&originWidth=1413&originalType=binary&ratio=1&size=173087&status=done&style=none&taskId=u44d39ddc-cbb2-4334-8a6e-b562507378a&width=706.5)

 
​


记下这个url的前半部分，等会有用的。
​

2.3 mitmproxy配置
首先，在PC端通过pip install mitmproxy安装mitmproxy模块，安装好后，继续通过cmd，输入mitmproxy，这样就会在系统盘的用户目录下的.mitmproxy文件夹下生成许多证书，点击mitmproxy-ca-cert.p12，选本地计算机，直接点下一步，设置密码的位置不用填，直接下一步，证书照下图放：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993409947-df4cc169-64f2-448d-b9c8-244e737c95e4.png#clientId=u5e3c67b1-925f-4&from=paste&height=227&id=u94a53fc6&name=image.png&originHeight=454&originWidth=633&originalType=binary&ratio=1&size=38371&status=done&style=none&taskId=u3ac51967-0365-49d1-be83-e248bb7893d&width=316.5)

 
​


这样PC端的证书就配好了，然后配夜神的，将mitmproxy-ca-cert.pem直接拖到夜神模拟器中，还是按照上面安装filder夜神模拟器端的证书方法操作，安装证书，然后创建douyin_fans.py文件：
​

```http
import json
 
 
def response(flow):
    if 'api3-normal-c-hl.amemv.com/aweme/v1/user/follower/list' in flow.request.url:
        for user in json.loads(flow.response.text)['followers']:
            user_info = dict()
            user_info['nickname'] = user['nickname']
            user_info['share_id'] = user['uid']
            user_info['douyin_id'] = user['short_id']
            user_info['sec_uid'] = user['sec_uid']
            if user_info['douyin_id'] == '0':
                user_info['douyin_id'] = user['unique_id']
                user_info['update_time'] = user['unique_id_modify_time']
            print(user_info)
```


然后配置夜神的wifi的代理，同样的8888端口和ip，关掉域防火墙，然后在douyin_fans.py文件目录打开命令行输入mitmdump -p 8888 -s douyin_fans.py，然后打开夜神上的抖音进入到粉丝里面，就可以看到命令行有输出了，如下图：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993450354-43bafe18-4488-41b2-b1f6-4c750d799003.png#clientId=u5e3c67b1-925f-4&from=paste&height=408&id=u38980cd9&name=image.png&originHeight=815&originWidth=1525&originalType=binary&ratio=1&size=288250&status=done&style=none&taskId=u7fcb9f8e-a7d6-4a43-9391-3f9213c0a18&width=762.5)

 
​


到此处，mitmproxy已经配置好了，而且可以抓到粉丝了，但是需要手动滑粉丝列表才能进行翻页，这里就需要使用Appium进行自动滑动粉丝列表了。
​

2.4 配置Android Studio
这里先解释一下为什么要下载Android Studio，主要是夜神模拟器是Android系统，而用Appium控制Android系统就需要Android的SDK，而现在的Android SDK与Android Studio是绑定的，安装好Android Studio后，按下图操作：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993469080-e1a7a170-d3bd-4adb-8bde-28172864ad30.png#clientId=u5e3c67b1-925f-4&from=paste&height=140&id=u08b3137b&name=image.png&originHeight=279&originWidth=578&originalType=binary&ratio=1&size=64533&status=done&style=none&taskId=u318e599a-1edb-44a8-ad28-304ea983249&width=289)

 
​


进入SDK Manager，安装Android5.1，并记住skd所在的路径：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993481204-0de59004-57f8-4bc3-8738-2c7dc3f22af1.png#clientId=u5e3c67b1-925f-4&from=paste&height=189&id=ub084ae6c&name=image.png&originHeight=377&originWidth=728&originalType=binary&ratio=1&size=40144&status=done&style=none&taskId=u8574a705-5ac2-47f4-947a-f2f436b8e5e&width=364)

 
​


安装好SDK后，进入对应的路径，然后打开系统的环境变量配置ANDROID_HOME以及一些path：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993493006-bad944a1-fcc1-4727-afc9-39199afb1b54.png#clientId=u5e3c67b1-925f-4&from=paste&height=333&id=u77bc0583&name=image.png&originHeight=666&originWidth=632&originalType=binary&ratio=1&size=47258&status=done&style=none&taskId=u78f5d2a6-d99a-4871-9168-f6442bc1044&width=316)

 
​


​

然后将platform-tools下的adb.exe文件放到夜神模拟器的bin目录中，与nox_adb.exe文件替换，并改为相同的名称nox_adb.exe，到此Android SDK的环境全部配完。
​

JDK的配置
安装好jdk后，在系统变量中配置JAVA_HOME，以及在path中添加jdk的bin路径，jdk的环境就配好了。
​

配置Appium
安装好Appium后直接打开，启动服务：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993509201-af7a8eaa-5b48-4ddf-adca-a5e7b11951ca.png#clientId=u5e3c67b1-925f-4&from=paste&height=300&id=u14342c44&name=image.png&originHeight=600&originWidth=650&originalType=binary&ratio=1&size=65478&status=done&style=none&taskId=ub7368f90-de34-4734-9589-bda9c95e773&width=325)

 
​


​

platformName:平台名称，一般是Android或iOS.
​

deviceName:设备名称，手机的具体类型
​

appPackage:App程序包名
​

appActivity:入口Activity名，通常以.开头
​

adb shell
​

dumpsys window |grep mCurrentFocus
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993539335-6f4404fb-50ea-44c1-baef-c627034df6b9.png#clientId=u5e3c67b1-925f-4&from=paste&height=91&id=u3e5ffb82&name=image.png&originHeight=182&originWidth=853&originalType=binary&ratio=1&size=15210&status=done&style=none&taskId=uf51232ae-e049-4ed8-9270-6984d17003e&width=426.5) 

 
​


然后点击放大镜，然后按图配置如下参数：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993530951-d7857f5a-8178-4f2e-b7f7-7f4a913bd990.png#clientId=u5e3c67b1-925f-4&from=paste&height=338&id=u5c2815c5&name=image.png&originHeight=675&originWidth=1048&originalType=binary&ratio=1&size=71421&status=done&style=none&taskId=u9cab3b55-0c4f-4c0a-b0a9-fac1f58bcee&width=524)

 
​


然后save，然后start session，这样夜神就会自动打开抖音，到此所有环境配置完毕，下面开始页面元素定位以及爬取的相关工作。
​


### 3. 定位页面元素
打开cmd输入monitor，等会就会出现一个界面，然后打开夜神的抖音，进行如下操作：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993552918-6f57915f-6bd8-4419-b152-709e7b035002.png#clientId=u5e3c67b1-925f-4&from=paste&height=318&id=u3733ed92&name=image.png&originHeight=635&originWidth=1838&originalType=binary&ratio=1&size=431217&status=done&style=none&taskId=u6057a6ca-3774-453e-876c-e40a8a0b262&width=919)

 
​


从左到右依次操作，这样就可以得到页面元素放大镜的id，后面的账号搜索点击元素都用同样的方法一步一步的得到所有元素的id值，编写douyin_appium.py文件：
​

```http
from appium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
import time
 
 
option = {
    "platformName": "Android",
    "platformVersion": "Andriod5.1.1",
    "deviceName": "127.0.0.1:62001",
    "appPackage": "com.ss.android.ugc.aweme",
    "appActivity": "com.ss.android.ugc.aweme.main.MainActivity",
    "noReset": True,
    "unicodekeyboard": True,
    "resetkeyboard": True,
    "automationName": "UiAutomator1"
}
 
driver = webdriver.Remote('http://localhost:4723/wd/hub', option)
 
try:
    if WebDriverWait(driver, 5).until(lambda x: x.find_element_by_id('com.ss.android.ugc.aweme:id/c63')):
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/c63').click()
except:
    pass
 
def get_size():
    x = driver.get_window_size()['width']
    y = driver.get_window_size()['height']
    return (x, y)
 
try:
    if WebDriverWait(driver, 3).until(lambda x:x.find_element_by_id('com.ss.android.ugc.aweme:id/ai4')):
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/ai4').click()
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/ai4').send_keys('lv123052')
        if WebDriverWait(driver, 3).until(lambda x: x.find_element_by_id('com.ss.android.ugc.aweme:id/ghv')):
            driver.find_element_by_id('com.ss.android.ugc.aweme:id/ghv').click()
    if WebDriverWait(driver, 2).until(lambda x: x.find_element_by_id('android:id/text1')):
        driver.find_element_by_id('android:id/text1').click()
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/ckr').click()
except:
    pass
 
try:
    if WebDriverWait(driver, 5).until(lambda x:x.find_element_by_id('com.ss.android.ugc.aweme:id/bce')):
        driver.find_element_by_id('com.ss.android.ugc.aweme:id/bce').click()
        time.sleep(2)
except:
    pass
 
size = get_size()
x1 = int(size[0]*0.5)
x2 = int(size[0]*0.7)
y1 = int(size[1]*0.9)
y2 = int(size[1]*0.2)
 
while(True):
    time.sleep(0.5)
    driver.swipe(x1, y1, x2, y2)
```



### 4. 运行项目
夜神的代理打开，域防火墙关掉，然后用mitmdump命令运行douyin_fans.py文件，这里得说明一下，mongdb数据库的操作在douyin_fans.py文件中自行添加，然后直接run，douyin_appium.py文件，这样就会自行爬取抖音的某账号粉丝信息，效果如图：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628993584985-c351ffd4-cf1f-4dcb-927b-32b3bd91ed4d.png#clientId=u5e3c67b1-925f-4&from=paste&height=265&id=u42fb09e0&name=image.png&originHeight=529&originWidth=1545&originalType=binary&ratio=1&size=193584&status=done&style=none&taskId=u92a93826-0f46-4395-993e-7cc70cb7ed3&width=772.5)

 
​


出于用户隐私保密，这里就将相关id打马赛克了，到此整个项目全部结束，如果您觉得不错的给个赞谢谢，创作不易呀。

[
](https://blog.csdn.net/weixin_42223833/article/details/110009432)



___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
