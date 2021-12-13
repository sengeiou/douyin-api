# 抖音爬虫入门，Appium+Mitmproxy强势组合实现抖音的数据爬取

APP爬虫入门，Appium+Mitmproxy强势组合实现抖音的数据爬取
>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

最近一直在研究APP的爬虫实现。我们就以最火的抖音为例子做一个演示例程。当然我们选择抖音并不是因为抖音火，主要是因为手上有一个小项目是基于抖音APP的，这个是后话🤭。
抓包APP思路和网页抓包是一个道理。最简单和效率最高的的方式通过直接分析网页请求。通过构造网页请求模拟发送取得返回值，从返回数据提取出需要的字段。这和网页抓包的思路是一样的。难点就在于网页抓包的请求参数构造都可以通过分析JS在脚本中模拟出来。但是对于APP而言，请求的参数构造是在APP内部进行的，我可没有像逆向大神的能力可以逆向APP，所以这条路行不通😔。这也是我们需要通过Appium的主要原因。对比起来这和不会分析JS而使用Selenium完全是一个道理啊( ﹁ ﹁ ) ~→。

## 实现目标
既然是入门例程，那么例子就相对简单，只是对这几天的学习做一个大概的总结。即便是这样我们也要实现一定的功能，不然一些毫无意义的代码只是浪费时间。那让我来🤔想想实现什么功能呢?重复的轮子我们不造，前段时间抓取抖音漂亮小姐姐的文章很火，当然我们才不要模仿他。有了，就试着写一个爬取指定抖音用户的粉丝的爬虫吧，顺便调用百度AI接口完成对粉丝颜值的打分！
显而易见，通过用户的头像对用户进行打分是一个很愚蠢的行为。大部分的粉丝并不是用自己的照片作为头像，甚至很多人的头像都不是人。所以这个功能就显得十分鸡肋。总之思路有了，有需要的朋友可以给数据增加更多的权重判断，来提高精准度。例如说：粉丝数量，收到的点赞数等等...一般这些数据都与博主的颜值都是正相关的，毕竟这确实是一个看脸的时代。
思路分析
通过appium进入指定用户的粉丝页面->自动执行遍历粉丝操作。然后mitmproxy作为中间人拦截，对返回的数据包进行处理，调用百度的人脸识别库对图像进行甄别。数据标注保存。思路很清晰，代码也很清晰，因为手头上没有真机，这个教程选择在虚拟机上测试，在虚拟机上速度肯定是要大打折扣的。
开发环境的搭建有点繁锁，不懂的可以看看之前的文章，有较为详细的介绍。但是真正的业务代码是真的挺简单的。这也是Python强大的体现，拥有庞大的第三方支持库。

## 代码实现

![](https://cdn.nlark.com/yuque/0/2020/png/97322/1607305992085-f8e130ba-2f1b-47a4-b994-1eb3839294fe.png#align=left&display=inline&height=432&originHeight=509&originWidth=697&size=0&status=done&style=none&width=592)
大致业务流程


---

> 环境支持：Python 3.6.5、Appium、MitmProxy
> 支持库：requests，Appium-Python-Client，mitmproxy （用pip install XXX依次安装即可）

> 虚拟机环境：Genymotion，Android 5.1.1

> 抖音版本：Ver 6.3.0



最后提取的数据以Json格式保存，每一条数据包括四个字段，分别是：**shortid（抖音号）**，**nickname（昵称）**，**uid（抖音用户内部主键）**，**beauty（颜值）**。第四个值有很大的不确定性，因为代码实现的并不完美，有需求的可以根据自己实际情况修改，本文章之作入门学习使用。代码主要分为两个只要模块，一个Appium控制手机，一个Mitmproxy抓取数据，我们分两个部份讲解代码。但是分析代码之前需要简单的介绍一下Mitmproxy。
MitmProxy的简易教程
mitmproxy 就是用于 MITM 的 proxy，MITM 即中间人攻击（Man-in-the-middle attack）。用于中间人攻击的代理首先会向正常的代理一样转发请求，保障服务端与客户端的通信，其次，会适时的查、记录其截获的数据，或篡改数据，引发服务端或客户端特定的行为。不同于Fiddler等抓包工具，mitmproxy不仅可以截获请求帮助开发者查看、分析，更可以通过自定义脚本进行二次开发。mitmproxy的python支持库可以让我在Python中截获数据包，这也是本次爬虫的基础。
关于mitmproxy的使用这次爬虫会给一个小小的教程，但是详细的教程还要自己去Mitmproxy的[Github](https://github.com/mitmproxy/mitmproxy/tree/master/examples)仓库查看，官方给了完整的examples，从简入繁，很适合新手学习。对于看英文文档有困难的同学，我同样找到一篇国内大神总结的文章：[使用 mitmproxy + python 做拦截代理-狼煞博客](https://blog.wolfogre.com/posts/usage-of-mitmproxy/)，一篇总结的很到位的入门教程，先弄懂了工作原理再去看官方例程会有种茅塞顿开的感觉。

---


Appium部份
![](https://cdn.nlark.com/yuque/0/2020/gif/97322/1607305992101-a9cc529f-16a0-4881-9305-43f97301a101.gif#align=left&display=inline&height=492&originHeight=825&originWidth=476&size=0&status=done&style=none&width=284)
Appium需要实现的效果


---

```python
def init_device():
    desired_caps = {}
    desired_caps['platformName'] = 'Android'
    desired_caps['udid'] = "192.168.13.107:5555"
    desired_caps['deviceName'] = "second"
    desired_caps['platformVersion'] = "5.1.1"
    desired_caps['appPackage'] = 'com.ss.android.ugc.aweme'
    desired_caps['appActivity'] = 'com.ss.android.ugc.aweme.main.MainActivity'
    desired_caps["unicodeKeyboard"] = True
    desired_caps["resetKeyboard"] = True
    desired_caps["noReset"] = True
    desired_caps["newCommandTimeout"] = 600
    device = webdriver.Remote('http://127.0.0.1:4723/wd/hub', desired_caps)
    device.implicitly_wait(3)
    return device
def move_to_fans(device):
    # 进入搜索页面搜索抖音号并进入粉丝页面
    device.find_element_by_id("com.ss.android.ugc.aweme:id/au1").click()
    device.find_element_by_id("com.ss.android.ugc.aweme:id/a86").send_keys(AIM_ID)
    device.find_element_by_id("com.ss.android.ugc.aweme:id/d5h").click()
    device.find_elements_by_id("com.ss.android.ugc.aweme:id/cwm")[0].click()
    device.find_element_by_id("com.ss.android.ugc.aweme:id/adf").click()
def fans_cycle():
    fans_done = []
    while True:
        elements = device.find_elements_by_id("com.ss.android.ugc.aweme:id/d9x")
        all_fans = [x.text for x in elements]
        if reduce(lambda x, y: x and y, [(x in fans_done) for x in all_fans]) and fans_done:
            print("遍历结束, 将会终止session")
            break
        for element in elements:
            if element.text not in fans_done:
                element.click()
                time.sleep(2)
                device.press_keycode("4")
                time.sleep(1)
                fans_done.append(element.text)
                print(element.text)
        device.swipe(600, 1600, 600, 900, duration=1000)
        if len(fans_done) > 30:
            fans_done = fans_done[10:]
```


Appium主要的职责是通过抖音号搜索用户，然后进入用户的粉丝页面，通过fans_cycle方法遍历整个粉丝列表。在遍历粉丝的时候APP会向服务器发送数据包，我们只需要在mitmproxy处理函数拦截response数据，对数据进行自定义修饰就行了。
**注意：测试发现不同的抖音版本的elements ID都是不同的，所以上述的代码不具有普遍性，或许需要重新用Appium获取元素ID。**
代码中标注的几行代码是做粉丝页面是否到底的判断处理。这里有必要解释一下，因为代码写的实在是太抽象了。我的思路是创建一个临时列表用于存储已经遍历过的粉丝，当取到一页粉丝的数据都在这个临时列表的时候即表明：该页数据没有刷新，就证明页面已经到底啦。这时就可以把循环终止掉了。但是在循环第一次执行的时候，两个列表都是空的，我们需要增加一个判空操作。reduce函数做的就是新取的数据是否全部存在于这个临时列表，成立为真，否则为假。这个临时列表会在每次循环做一次切片操作，保证长度不超过30，节约系统内存。
这个判断并不是很好理解，但是思想确实很简单的，Python语言的精炼也体现出来了，如果这个是Java的话我也许要写几十行才能完成这个相同的需求。总之一句话：**人生苦短，我用Python**
Mitmproxy部份
这部分包括了数据拦截和百度API调用。通过拦截抖音的数据包请求进行过滤，找出用户详情的数据包截获response数据请求。因为返回的数据都是Json类型，所以在Python内十分容易解析。我们需要的四个字段都在数据包里，其中抖音号可能为空。对于用户颜值打分，我们统一使用用户的高清大图头像。
```python
import mitmproxy.http
import json
from spider.api.baidu import FaceDetect
from lib.shortid import Short_ID
face = FaceDetect()
spider_id = Short_ID()
class Fans():
    def response(self, flow: mitmproxy.http.flow):
        if "aweme/v1/user/?user_id" in flow.request.url:
            user = json.loads(flow.response.text)["user"]
            short_id = user["short_id"]
            nickname = user['nickname']
            uid = user["uid"]
            avatar = user["avatar_larger"]["url_list"][0]
            beauty = face(avatar)
            short_id = spider_id(uid) if short_id == "0" else short_id
            data = {
                "short_id": short_id,
                "nickname": nickname,
                "uid": uid,
                "beauty": beauty
            }
            print(data)
```


百度AI人脸识别，由于比较简单，可以通过API文档编写出我们需要的代码，这里就不做太多的赘述了（[文档地址](https://ai.baidu.com/docs#/Face-Detect-V3/top)）,这里贴出我的请求代码供有需要的朋友学习。由于用户头像中可能不止有一张人脸，所以我们对多张人脸图像做了平均颜值处理，得出的颜值数据为平均值，保留四位小数。注意：如果颜值为0并不代表非常ugly，或许是头像中没有人脸，或者是api请求达到请求上限，请结合实际做出判断，有需要的可以自行完善代码。
```python
import requests
import json
class FaceDetect():
    def __init__(self):
        self.ak = "百度acess_key"
        self.sk = "百度secret_key"
        self.token = self.__access_token()
    def __access_token(self):
        url = 'https://aip.baidubce.com/oauth/2.0/token?' \
              'grant_type=client_credentials&client_id={}&client_secret={}'.format(self.ak, self.sk)
        headers = {'Content-Type': 'application/json; charset=UTF-8'}
        req = requests.get(url, headers=headers)
        token = json.loads(req.text)["access_token"]
        return token
    def __face_detect(self, pic):
        url = "https://aip.baidubce.com/rest/2.0/face/v3/detect?access_token={}".format(self.token)
        params = {
            "image": pic,
            "image_type": "URL",
            "face_field": "age,beauty,expression,gender,face_shape,emotion",
            "max_face_num": "10"
        }
        req = requests.post(url, params=params)
        return req.text
    def __average_beauty(self, data):
        if data["error_code"] == 0:
            average_beauty = []
            for face in data["result"]["face_list"]:
                average_beauty.append(face["beauty"])
            return "{:.4f}".format((sum(average_beauty) / len(average_beauty)))
        return 0
    def __call__(self, url):
        r = self.__face_detect(url)
        data = json.loads(r)
        return self.__average_beauty(data)
```



---


![](https://cdn.nlark.com/yuque/0/2020/gif/97322/1607305992132-57f695ec-b4ee-4136-a393-374352ece36d.gif#align=left&display=inline&height=177&originHeight=214&originWidth=1007&size=0&status=done&style=none&width=833)
运行输出效果图


## 总结
总的来说这个文章涉及的知识还是蛮多的，需要慢慢消化。

