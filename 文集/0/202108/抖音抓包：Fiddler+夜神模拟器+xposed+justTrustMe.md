## 抖音抓包：Fiddler+夜神模拟器+xposed+justTrustMe

一、Fiddler下载安装Fiddler是一款抓包神器，可以让电脑、手机、平板上所有应用发送的请求先通过Fiddler再发送到服务器，服务器返回的应答先通过Fiddler再返回给应用，相当于一个邮寄员。


需要科学上网注册账号才能用，可以去网上找其他破解版安装。
​


---



需要数据采集接口SDK请[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)

---

​

1.Live Traffic是抓取开关，打开后电脑所有的请求就经过Fiddler代理，Fiddler就开始记录并抓取所有的请求。关闭Live Traffic或关闭Fiddler软件会退出Fiddler代理。
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628553927123-22e17516-b6fb-4087-9090-5aca6137fe5c.png#clientId=u6c97f90f-91ba-4&from=paste&height=321&id=ufb9ce517&name=image.png&originHeight=642&originWidth=1832&originalType=binary&ratio=1&size=73865&status=done&style=none&taskId=u840955ac-d548-49d6-bc84-18e46493a8e&width=916)

 
​


2.勾选Capture HTTPS trafficc后重启才能抓取https请求。
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628553943345-157e00dd-109a-4be3-a83f-0e477fe34340.png#clientId=u6c97f90f-91ba-4&from=paste&height=223&id=ua96ae826&name=image.png&originHeight=446&originWidth=737&originalType=binary&ratio=1&size=28100&status=done&style=none&taskId=ua2acc095-1869-40a3-9144-60cf81c2b44&width=368.5)

 
​


3.端口默认是8866，可自行修改，勾选Allow remote computers to connect才能抓取手机其他设备的包。
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628553973887-0c24b840-f872-4fda-aad5-e8cb495dd390.png#clientId=u6c97f90f-91ba-4&from=paste&height=557&id=udaad19fd&name=image.png&originHeight=1114&originWidth=1566&originalType=binary&ratio=1&size=186122&status=done&style=none&taskId=u7337e10b-ddaf-4089-aa8e-25be6eca80d&width=783)

 
​


二、下载夜神模拟器
问题：为什么要下载夜神模拟器+xposed+justTrustMe？
​

答： 这就要谈到『SSL Pinning』技术。App 可以自己检验 SSL 握手时服务端返回的证书是否合法，“SSL pinning” 技术说的就是在 App 中只信任固定的证书或者公钥。 因为现在手机安卓系统7.0以上把证书分为系统证书和用户证书。
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554001926-62c2c91b-c1d2-4f4b-91e2-6d8995929781.png#clientId=u6c97f90f-91ba-4&from=paste&height=405&id=u83fa5498&name=image.png&originHeight=810&originWidth=385&originalType=binary&ratio=1&size=62983&status=done&style=none&taskId=ub9f27855-fd69-4ec9-b4f9-b80118c3914&width=192.5)

 
​


有些app默认只信任系统证书，而用真实手机抓包的话有些软件会提示没网（比如抖音）。所以需要使用 Android 下的一些 Hook 技术来绕过本地证书强校验的逻辑，比如我们使用的模拟器+xposed+justTrustMe，
​

以小米手机为例，在手机设置->密码与安全->系统安全->加密与凭据->信任的凭据中可以看到系统证书和用户证书：
​

​

所以用模拟器模拟一个低版本的安卓系统，而且下面安装xposed需要root权限，弄不好手机会变砖，所以模拟机比较放心。
​

另外一点需要注意的是，如果用模拟器登录抖音，第一次可能没问题.第二次会出现推荐为空，无法浏览视频之类的问题，因为用模拟器登录时定位可能变化的太快，导致抖音禁ip,如果退出再登录会显示操作频繁。
​

1.下载安装模拟器
​

[夜神模拟器官网下载](https://www.yeshen.com/)
​

2.安装好后打开夜神多开器
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554035780-1b912e79-49c7-4f29-95ff-4d12ed118a1f.png#clientId=u6c97f90f-91ba-4&from=paste&height=264&id=u7b69ee54&name=image.png&originHeight=527&originWidth=793&originalType=binary&ratio=1&size=33072&status=done&style=none&taskId=u8211309b-ad42-4651-9533-e4689c3cd11&width=396.5)

 
​


3.选择安卓5.0
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554065574-17359b21-acdc-4ff9-9598-db7c58a6230b.png#clientId=u6c97f90f-91ba-4&from=paste&height=258&id=ua1a71ba0&name=image.png&originHeight=515&originWidth=785&originalType=binary&ratio=1&size=36298&status=done&style=none&taskId=uf142610b-5cf8-4762-90e3-2b87bf512b7&width=392.5)

 
​


安装xposed
Xposed 框架是一款可以在不修改 APK 的情况下影响程序运行（修改系统）的框架服务，基于它可以制作出许多功能强大的模块，且在功能不冲突的情况下同时运作.
（安装过程请自行百度）
​


将软件拖到上面的手机模拟器中安装，安装后如图点击下载xposed框架：

 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554088449-33fc5e81-625b-4d8f-b9ab-ada35b08039b.png#clientId=u6c97f90f-91ba-4&from=paste&height=394&id=ue1328cff&name=image.png&originHeight=787&originWidth=1386&originalType=binary&ratio=1&size=101567&status=done&style=none&taskId=u5c193161-8a46-4c6d-bf38-3d30ef60139&width=693)

 
​


点击安装等待重启，期间会提示需要root权限。
​

安装justTrustMe（安装过程请自行百度）
​

将软件拖到上面的手机模拟器中安装。
​

夜神模拟器网络配置
1.首先查看电脑ip,可使用命令行ipconfig查看或者点击抓包软件Fiddler右下角网络图标查看
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554144031-a73949c6-3884-468d-8462-bae6aa0ec76e.png#clientId=u6c97f90f-91ba-4&from=paste&height=346&id=uc1cc41a1&name=image.png&originHeight=692&originWidth=1151&originalType=binary&ratio=1&size=29874&status=done&style=none&taskId=u34b90627-8d13-4467-b644-4c460e8d7ef&width=575.5)

 
​


2.模拟器设置里找到WLAN网络设置，鼠标左键长按选择修改网络
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554159796-d36008d8-166a-488f-911c-2eeec648e899.png#clientId=u6c97f90f-91ba-4&from=paste&height=400&id=ue36f9718&name=image.png&originHeight=800&originWidth=1410&originalType=binary&ratio=1&size=54135&status=done&style=none&taskId=u9105a511-15a2-406c-a48e-c95ad9affba&width=705)

 
​


3.代理选手动，输入电脑ip（必须确保模拟器和电脑在同一网络才能抓到包）和抓包软件Fiddler设置里的端口号。
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554173892-73e86d47-c97f-4163-a5e4-9d83d17e31a8.png#clientId=u6c97f90f-91ba-4&from=paste&height=373&id=ub304b0c8&name=image.png&originHeight=746&originWidth=959&originalType=binary&ratio=1&size=56200&status=done&style=none&taskId=u2635cbae-359f-49ef-95fa-d1d77ab3953&width=479.5)

 
​


4.电脑Fiddler启动，打开Live Traffic抓取开关，然后模拟器的浏览器打开http://你的电脑ip:Fiddler端口号。
​

比如我的是：[http://192.168.137.1:8866](http://192.168.137.1:8866)
​

然后点击FiddlerRoot certificate下载Fiddler证书。
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554189478-77c97206-30dd-4c63-a5f0-39e4ed1e5f3f.png#clientId=u6c97f90f-91ba-4&from=paste&height=431&id=u9a81b413&name=image.png&originHeight=861&originWidth=1306&originalType=binary&ratio=1&size=118413&status=done&style=none&taskId=u6a18096f-373e-404c-9ef4-68a7f39be85&width=653)

 
​


下载完后打开证书，然后你可以为证书重命名，然后可能会需要你设置锁屏密码，然后证书就安装成功了。
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554205353-af890b8d-b4d4-46d3-816c-47e7a2eddc35.png#clientId=u6c97f90f-91ba-4&from=paste&height=321&id=u8b4ee72b&name=image.png&originHeight=641&originWidth=978&originalType=binary&ratio=1&size=42478&status=done&style=none&taskId=u747f0649-6abc-43f1-bf37-afcb644fea5&width=489)

 
​


接下来在模拟器里运行软件，比如抖音，就可以在Fiddler中抓到包了。
以抖音为例子：
​



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554222565-06dcd808-0008-4039-a2ce-41a2d8d49ab3.png#clientId=u6c97f90f-91ba-4&from=paste&height=296&id=u892f7eb9&name=image.png&originHeight=591&originWidth=350&originalType=binary&ratio=1&size=436570&status=done&style=none&taskId=ueca4fab7-ac0b-4d45-8f0c-de424d27d1e&width=175)

 
​



抖音这个app其实一开始是叫做A.me，也就是英文Awesome Me 崇拜我的意思，后来改名为抖音。抓到的请求aweme开头的就是抖音的请求了。

 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628554239190-5a6d5d27-ee9d-45cb-a108-ec2a8ec657ca.png#clientId=u6c97f90f-91ba-4&from=paste&height=472&id=u1ea322f3&name=image.png&originHeight=944&originWidth=1691&originalType=binary&ratio=1&size=289428&status=done&style=none&taskId=u99a2c052-efa8-4a45-be93-fed7d3573cc&width=845.5)

 



注意：
因为证书的原因或者可能因为抖音本身防抓的原因，使用模拟器抓包第一次登陆看视频抓包都没啥问题，但是过两天或者第二次打开模拟器就不行了。
解决办法：
删除配置好的的模拟器，再按本教程从头重新配置一个就好了。


[
](https://blog.csdn.net/qq_33697094/article/details/111596004)

___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
