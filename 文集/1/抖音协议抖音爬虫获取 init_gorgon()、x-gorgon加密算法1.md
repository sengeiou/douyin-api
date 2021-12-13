# 抖音协议抖音爬虫获取 init_gorgon()、x-gorgon加密算法


 
1、用到的工具等：
Charles (随便一个抓包工具即可，哪个顺手用哪个)
动态字段： x-gorgon：0408*** （开头）
抖音Version：12.8.0 （发帖时的最新版本）or 抖音极速版 （文件少，编译快）
IDA or JEB
Jadx-gui
frida
Pycharm
root 真机（Android） or 模拟器

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


2、反编译：
Apk 直接丢入 Jadx-gui or Apk==>zip ==> 解压 ==> .dex 全部打包丢入 Jadx-gui
查找关键字： x-girgon
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128046539-933da4e3-cc27-4aa6-86dd-93ecf6c9281f.png#align=left&display=inline&height=473&name=image.png&originHeight=946&originWidth=2400&size=1367033&status=done&style=none&width=1200)
先看调用处：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128057114-710e3d78-0e59-4e51-977a-fa4a327b4044.png#align=left&display=inline&height=445&name=image.png&originHeight=890&originWidth=1064&size=1062611&status=done&style=none&width=532)
这个so 文件全名： [libcms.so](http://libcms.so/) apk压缩包中可以找到。
接下来看看函数声明处：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128102698-37dc7468-4215-4a4b-872d-fa27ef852354.png#align=left&display=inline&height=508&name=image.png&originHeight=1016&originWidth=1654&size=1463394&status=done&style=none&width=827)
代码不能被工具完美还原，试了几个工具都这球样，不用浪费时间折腾了。直接看吧，功底好的可以直接看Smali 代码。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128115381-e157adc7-e4aa-4fa1-9273-8c182e38beb1.png#align=left&display=inline&height=165&name=image.png&originHeight=330&originWidth=698&size=322696&status=done&style=none&width=349)
可以看到r8 即为想要的值，向上查找r8
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128129734-a0826547-1837-46d3-86be-d56d9d2a48b8.png#align=left&display=inline&height=191&name=image.png&originHeight=382&originWidth=1176&size=786052&status=done&style=none&width=588)
其中 com.ss.a.b.a.a() 是重载函数，hook时需要注意下。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128140107-e96c0dec-ff0e-41f1-b9f1-57c9651674ec.png#align=left&display=inline&height=227&name=image.png&originHeight=454&originWidth=1552&size=606008&status=done&style=none&width=776)


 

 

贴附一段Frida 代码： 注意overload("")
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128150940-09796c72-5b65-4b53-b7cb-8426f8405a44.png#align=left&display=inline&height=251&name=image.png&originHeight=502&originWidth=1192&size=433837&status=done&style=none&width=596)
这块儿的代码是可以正常展示的，很好还原：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128165446-2a01b9a1-8da6-4509-9f0e-da0db4af2993.png#align=left&display=inline&height=609&name=image.png&originHeight=1218&originWidth=2588&size=1580962&status=done&style=none&width=1294)
接下来到了难点：
byte[] r0 = com.ss.sys.ces.a.leviathan(r8, r7, r0)
这个不太好还原， 三个参数：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128183653-6b02c188-fc83-4269-b5b0-10588cf77d3c.png#align=left&display=inline&height=294&name=image.png&originHeight=588&originWidth=956&size=488646&status=done&style=none&width=478)
native 修饰的 leviathan （）函数， 对应的方法体在最早加载的 [cms.so](http://www.cms.so/) 中。
可以使用ida or jeb 进行查看，会发现没有到处函数，其使用了花指令混淆的加密措施。
破解难度太大，可以考虑换了一种简单的调用方式进行解决
优先推荐 ： frida-rpc, 实现思路如下： 具体的自行完善
(我自己使用的java 进行主动调用的，想了解的可以关注后留言，或者微信)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128226473-d68bd4d0-5ff7-444f-a59c-5481a14c7446.png#align=left&display=inline&height=207&name=image.png&originHeight=414&originWidth=1400&size=419251&status=done&style=none&width=700)
这种方式比较简单，还可以开微服务。
其中传递进去的参数可以使用frida hook 进行获取
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128241862-74c60395-c516-400e-8a50-b5e3108d7fc2.png#align=left&display=inline&height=98&name=image.png&originHeight=196&originWidth=1276&size=331458&status=done&style=none&width=638)
输出结果为：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128254906-f3d98313-3ac5-4633-a00d-36ce9f943b89.png#align=left&display=inline&height=146&name=image.png&originHeight=292&originWidth=1574&size=223258&status=done&style=none&width=787)
参数1: -1
参数2: 十位时间戳（来自url参数中的十三位时间戳）同 x-khronos 相同。
参数3: post参数的data 部分，就是下图中的r0
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607128267306-c39fe6d1-1462-4adf-abb9-aa74ab89adcf.png#align=left&display=inline&height=102&name=image.png&originHeight=204&originWidth=1014&size=422896&status=done&style=none&width=507)
**参数整理：**
r0 = md5(url？后的参数) 对网址的参数进行md5
r13 = x-ss-stub，只有post时才有效，否则是32个0
r11 = md5(cookie) 对cookie进行md5
r12 = md5(cookie['sessionid']) 对cookie里面对sessionid进行md5，否则也是32个0


 

___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
