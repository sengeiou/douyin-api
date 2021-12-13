# 小红书数据采集教程，从0到1 APP采集，Airtest


### 一、前言：
小红书的爬虫主要是从搜索入手，爬取某个关键词下的所有笔记，通过调研发现有有两个渠道，第一个是 App，第二个小程序。先说小程序端，通过抓包发现，每篇文章对应着一个 `auth-sign` ，这个参数应该有小程序内部生成，无法获取到，有种思路就是可以通过按键精灵或者 Mitmproxy 获取到每篇笔记的 `auth-sign` ，然后就可以通过接口去爬，这中间还需要一个自动化控制手机工具；再来说 `App` 端，需要有好几个加密参数 `sign\token\shield`，之前git上面还有大神维护着一个服务器可以破解签名，现在已经失效了，App 逆向暂时还没学（已提上日程），所以还是用手机端的自动化测试工具来爬，主要软件有Appium和Airtest，Appium 坑比较大，不仅安装麻烦而且调试时极不方便，再者获取不到小红书笔记的详情页的页面结构，本文主要讲下使用 Airtest + Mitmproxy 进行手机端的爬虫，这两者加起来，可以解决90%的 App 爬虫吧，除了淘宝...但可以用Pypeteer。

### 二 、Airtest
Airtest 是一个跨平台的、 **基于图像识别 **的`UI`自动化测试框架，适用于游戏和`App`，支持平台有Windows、Android 和 IOS，主要编写语言为 python。官网 -> [点我](http://airtest.netease.com/)， 官方文档 ->[ [点我]](http://airtest.netease.com/docs/cn/1_quick_start/0_intro.html) ，基本使用可以看下青南大佬的blog ->[ [点我]](https://www.kingname.info/2019/01/19/use-airtest/)<br>
<br>Airtest与Appium相比最大的优点是可以实时的看到页面的动态，而且支持图像识别进行点击等操作

### 三、使用Airtest自动化控制App
前期的使用可以在Airtest自带的IDE中调试，毕竟边操作边看到界面是非常方便地，等成熟之后搬到pycharm中执行再考虑作定时任务等<br>![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607827478782-c5ad3f7e-ecb9-47e2-acd3-fda5f3b69166.png#align=left&display=inline&height=509&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1018&originWidth=1920&size=549368&status=done&style=none&width=960)

### 四、Airtest基本[[Api](http://airtest.netease.com/docs/cn/6_poco_framework/poco_source/poco.pocofw.html)]使用：

1. 获取元素：
   - `ls = poco(name="com.xingin.xhs:id/aj8") 通过name获取`<br>
   - `ls = poco(text=item.get_text()) 通过text`<br>
   - `poco("android.widget.LinearLayout").offspring("com.xingin.xhs:id/ak6") 通过目录树`<br>
2. 点击元素： `x.click()`<br>
2. 获取文本： `x.get_text()`<br>
2. 滑动屏幕： `swipe([0.5, 0.8], [0.5, 0.7]) 从一个点到另一个点`<br>
2. 是否存在： `x.exist()`<br>
2. 安卓[ADB](http://airtest.netease.com/docs/cn/5_airtest_framework/airtest_all_module/airtest.core.android.adb.html)操作：
```python
from airtest.core.android.adb import ADB
adb = ADB(serialno='OF9SBMY59D69756L')
​
# 原始 adb shell 命令为 adb shell input keyevent 26
adb.start_shell('input keyevent 26') # 唤醒屏幕
​
# 启动app app包名
adb.start_shell('am start -n com.xingin.xhs/.activity.SplashActivity') 
# 获取app包名
aapt dump badging C:\Users\xx\Desktop\xiaohongshu.apk
# launchable-activity: name='com.xingin.xhs.activity.SplashActivity'  
其他 API
```
踩的一些坑

   - 官方的问题示例 能解决大部分问题 [[点我]](http://airtest.netease.com/docs/cn/9_faq.html)<br>
   - Airtest与pycharm中执行脚本互斥 不能同时开<br>
   - 点击时可能会有两种BUG出现，第一是点击无反应，只能作容错处理；第二是点击内容在屏幕之外，这时需要上滑屏幕，然后再做点击<br>

### 五、Mitmproxy
mitmproxy是一个支持HTTP和HTTPS的抓包程序，类似Fiddler、Charles的功能，只不过它通过控制台的形式操作；使用 mitmproxy 最主要是使用它的一个组件 mitmdump ，它可以通过python脚本处理响应内容，类似于Fiddler的界面抓包，但是我们可以更加方便地拿到响应数据<br>

1. 安装 -> [点我](https://cuiqingcai.com/5391.html)<br>
1. 使用：
```
mitmdump -s mitm.py   # 手机连接电脑ip：8080
```
```python
# 拦截某个链接
if flow.request.url.startswith(detail_url):
    text = flow.response.text # 获取链接的响应
```

   - 需注意每个链接都会经过这个方法<br>

### 六、小红书
主要逻辑：

1. 进入App：
   - 唤醒屏幕 `adb.start_shell('input keyevent 26')`<br>
   - 解锁<br>
   - 启动APP`adb.start_shell('am start -n com.xingin.xhs/.activity.SplashActivity')`<br>
2. 启动mitmproxy `mitmdump -s mitm.py`<br>
2. 模拟操作：
   - 获取当前界面列表页的所有商品，点击进入，mitm会捕获目标链接，在mitm.py处理数据<br>
   - 点击返回有三种方法：
      - 通过页面返回按钮，实测按钮会点击无反应；<br>
      - 通过Airtest的touch图像识别方法，需将图片放入同一路径下；<br>
      - 点击手机返回键，推荐！ `adb.start_shell('input keyevent 4')`<br>
4. 入库<br>
4. 退出程序，锁屏<br>
4. 补充：代码不多，主要是模拟人操作，然后加上容错判断，比如

`if poco(name="com.xingin.xhs:id/aon").exists()`             `poco.wait_for_any(poco(name="com.xingin.xhs:id/al0"))`<br>
<br>



>
> 短视频、直播电商数据采集、分析服务，请联系微信：1764328791
> 免责声明：本文档仅供学习与参考，请勿用于非法用途！否则一切后果自负。
> 
