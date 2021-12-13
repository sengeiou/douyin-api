# 抖音爬虫，当你写爬虫抓不到APP请求包的时候该怎么办？



这篇文章的主要内容是解决在遇到APP没有使用Android自带的HTTP客户端进行请求，并且对HTTP客户端的代码进行了混淆，导致通用工具JustTrustMe失效的问题。而中级篇中除了JustTrustMe以外的所有方法也都会对这种情况束手无策，原因是中级篇中的1、3、4方法本质上针对的是Android 7.0+系统增加的SSL Pinning方案，而无法对各个HTTP客户端自己实现的检测方案生效。（听说有个叫车速拍的APP就是这种类型呢）
那么应该怎么做才能抓到这类APP的包呢？很简单，依然是使用JustTrustMe之类的Hook工具就好了，只不过我们需要针对混淆后的名字对原来Hook的部分进行特殊处理。

---

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

 
这里我专门写了一个样例APP来进行演示（别抓我，我什么都不知道），这个APP做的事情就是在你点击了按钮之后，对百度首页发起一次请求，但是这个请求在没有破解SSL Pinning的正常情况下是不可能成功的，因为我设置的是一个随便输入的证书哈希，所以在请求的时候会因为正常的证书哈希与我随便输入的哈希不同导致出现请求失败的情况。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218800977-f5885f4d-db66-425c-a32d-65b7fc100bcb.png#align=left&display=inline&height=483&name=image.png&originHeight=966&originWidth=1236&size=570687&status=done&style=none&width=618)
样例APP代码
这个APP我已经编译好放到GitHub上了，有两个版本，一个是对代码进行过混淆的，一个是没混淆的，文末会有下载地址，读者可以下载下来自己玩玩。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218840179-1507d869-5ab1-49ce-b159-bab3785b9878.png#align=left&display=inline&height=119&name=image.png&originHeight=238&originWidth=1190&size=215359&status=done&style=none&width=595)
两个编译好的APK
开始演示之前说一下测试机的配置吧，这里用的测试机是Android 8.1.0的，已经Root+Xposed，同时已经安装并激活了JustTrustMe。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218863722-dc25bb44-4209-4f96-b79a-c74aafc5c284.png#align=left&display=inline&height=753&name=image.png&originHeight=1506&originWidth=784&size=328048&status=done&style=none&width=392)
测试机系统信息
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218879475-d10abf86-78e8-4a81-bab9-d260de0f3988.png#align=left&display=inline&height=71&name=image.png&originHeight=142&originWidth=890&size=103592&status=done&style=none&width=445)
Xposed模块管理界面-JustTrustMe已开启

 

---


 
我们先来看一下没混淆过代码的版本，装上之后打开它，然后点击“点击发送请求”按钮。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218893750-0d45e905-4c18-44b2-8059-c7fc293f0d5b.png#align=left&display=inline&height=748&name=image.png&originHeight=1496&originWidth=870&size=203330&status=done&style=none&width=435)
样例APP界面
不出意外的话会出现请求成功的字样，如果出现请求失败的话可能是你网络问题，证书问题会提示“证书验证失败”。
接下来我们看看混淆过代码的版本，操作同上。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218924050-6616aec5-98d2-4baa-a06f-8c80a81c76d0.png#align=left&display=inline&height=154&name=image.png&originHeight=308&originWidth=638&size=75750&status=done&style=none&width=319)
证书验证失败
这次就是证书验证失败了，JustTrustMe并没有正常生效。

 

---


 
我们将这两个APK都放到Jadx中反编译一下看看。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218940103-66fbac3c-12b7-4ece-84e7-0fee8ac15d70.png#align=left&display=inline&height=667&name=image.png&originHeight=1334&originWidth=2768&size=1458070&status=done&style=none&width=1384)在Jadx中反编译两个样例APK
可以看到混淆过的版本里，okhttp3下的所有类名已经变成了abcd这种名字。
然后我们来看一下JustTrustMe的代码。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218953787-2c355a19-d929-4756-9da2-7b2591f19838.png#align=left&display=inline&height=411&name=image.png&originHeight=822&originWidth=2298&size=1045421&status=done&style=none&width=1149)
JustTrustMe代码中Hook okhttp3的证书验证部分
可以看到它的代码中是对okhttp3.CertificatePinner类下的check方法进行Hook的，这个CertificatePinner类和check方法在没混淆过的APK中可以很清楚地看到。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218968272-c02672c4-f7fb-4b78-9495-b246412c5367.png#align=left&display=inline&height=507&name=image.png&originHeight=1014&originWidth=1724&size=1922842&status=done&style=none&width=862)
反编译检测代码1![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218983206-bcec7746-32d1-48ec-af6f-2f8447440824.png#align=left&display=inline&height=622&name=image.png&originHeight=1244&originWidth=2436&size=2726230&status=done&style=none&width=1218)
反编译检测代码2
那么现在JustTrustMe面对混淆后的版本就失效的原因已经很清晰了，因为它找不到这个okhttp3.CertificatePinner.check，所以根本就不可能Hook到检测方法，自然不会有任何效果。
所以...应该怎么办呢？这里依然是给出多种方法供读者选择：
1、修改JustTrustMe代码中Hook的类名和方法名然后重新编译
很简单，找到对应的检测方法，把JustTrustMe代码中Hook的className和methodName改成混淆后的名字即可，比如在这个混淆后的样例APP里，okhttp3.CertificatePinner.check变成了okhttp3.f.a。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607218996064-a65c2656-3733-4e6e-a71a-2246adc54aae.png#align=left&display=inline&height=306&name=image.png&originHeight=612&originWidth=1714&size=674247&status=done&style=none&width=857)
反编译检测代码-混淆后
我们修改JustTrustMe中的Hook部分，同样改为f和a。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607219006102-4cb2bf5c-55ec-4b45-801c-83867f3dfdcd.png#align=left&display=inline&height=379&name=image.png&originHeight=758&originWidth=1738&size=759714&status=done&style=none&width=869)
JustTrustMe对okhttp3的证书检测Hook![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607219018024-5aa47718-4166-4479-84b6-bf647d5f17d3.png#align=left&display=inline&height=226&name=image.png&originHeight=452&originWidth=1264&size=353090&status=done&style=none&width=632)


 

 

修改JustTrustMe对okhttp3的证书检测Hook
改完之后编译一下，安装到手机上替换原来的就好了。
2、使用Frida进行Hook
这个方法比方法1要更方便、更直接一些，因为需要的时候直接修改脚本马上就能用，不需要重新编译、重启手机或APP，这里直接拿[瘦蛟舞](https://link.zhihu.com/?target=https%3A//github.com/WooyunDota)大佬写的[解除SSL Pinning脚本](https://link.zhihu.com/?target=https%3A//github.com/WooyunDota/DroidSSLUnpinning/blob/master/ObjectionUnpinningPlus/hooks.js)修改一下，同样是修改Hook okhttp3.CertificatePinner.check的部分，改成混淆后的名字即可。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607219030973-9723329e-e886-48e5-965c-b896df99676c.png#align=left&display=inline&height=335&name=image.png&originHeight=670&originWidth=1486&size=373959&status=done&style=none&width=743)
修改瘦蛟舞的ObjectionUnpinningPlus脚本
3、魔改JustTrustMe，增加一个可以在运行时根据情况调整每种HTTP客户端的SSL Pinning检测部分的类名和方法名的功能
这个我暂时没空弄，感兴趣的同学可以自己实现一下。
4、魔改JustTrustMe，对Hook部分增加动态适配功能，做到即使开发者对代码进行了混淆也能自动找到真实的检测类和方法
同上，实现方式可以参考[微信巫师框架部分](https://link.zhihu.com/?target=https%3A//github.com/Gh0u1L5/WechatSpellbook)的自动适配代码，实现以后理论上来讲是最方便的办法之一，因为是完全自动的操作，不需要人工介入。
5、修改反编译APP得到的代码再打包回去
我觉得应该没人会用这么蠢的办法吧...用Hook的方式做起来要方便太多了。
选择任意一种方法操作后，再打开混淆版本的APP就可以正常请求了。

 

---


 
这时候可能会有同学要问了，怎么样知道APP用的是哪个HTTP客户端？又怎么样快速定位混淆后的检测方法位置呢？
很简单，我们先关掉破解工具，连上代理然后抓个包看看混淆版本APP的请求。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607219048048-46373907-95e6-4bd0-890b-9e362e6a393c.png#align=left&display=inline&height=540&name=image.png&originHeight=1080&originWidth=2780&size=1320537&status=done&style=none&width=1390)
抓包查看请求
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607219066215-5d1276e1-38c7-408d-acbe-458702b53e55.png#align=left&display=inline&height=125&name=image.png&originHeight=250&originWidth=850&size=203676&status=done&style=none&width=425)
User-Agent部分
和平时遇到SSL Pinning的情况一样，这里只会抓到一个CONNECT请求，注意右边的headers，从User-Agent中可以看出这个APP使用的是okhttp3，那么我们在混淆后的代码中定位检测部分代码的位置时，就只需要对照着okhttp3的原始代码来找就好了（其他HTTP客户端同理）。当然了，也不排除有些APP会把User-Agent改掉，如果从User-Agent上看不出来的话，那就看一下反编译出来的源代码部分结构，看看有没有像okhttp3之类的这种特别明显的HTTP客户端的名字，有的话就把它干掉就好了。


 

___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
