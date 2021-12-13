# 抖音API接口数据采集-APP数据采集常见思路，抖音直播间数据抓取

其实所谓抓取APP数据和抓取网页数据是存在一定的不同，抓取网页数据可以采用模拟访问网站然后抓取网页接收内容的模式进行数据抓取。而APP则更倾向于通过截获数据传输包的形式进行（Wireshark和Fiddler+Python）。

一般来说，我们用WireShark+Fiddler来获取大部分数据是没有多大问题的。但这里有个问题是，如果你碰到的是用SSL/TLS等加密手段加密过的网络数据的时候，往往我们只能束手无策。

在过去的话，如果我们拥有的该传输会话的私钥的话我们还是可以将它提供给WireShark来让其对这些加密数据包进行解密的，但这已经是想当年还用RSA进行网络数据加密的年代的事情了。

当今大家都已经逐渐拥抱前向加密技术PFS的时代了，所以该方法就不再适用了。

因为前向加密技术的目的就是让每个数据交互都使用的是不同的私钥，所以你像以前RSA时代一样想只用一个私钥就能把整个session会话的网络数据包都破解出来的话是不可能的了（其实可以也通过类似浏览器的Session Key功能解决）。

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 



## 1.App数据抓取分析
凡是可以看到的APP数据都可以抓取。
分析研究过不下300个APP抓包。
50%的app，通过抓包软件就可以分析出抓取参数并抓取到信息。
30%的app，可能需要适当的反编译，分析出加密算法并抓取到信息。
10%的app，可能加固，需要脱壳，然后反编译，分析出加密算法并抓取到信息。
10%的app，通过各式各样的签名，证书，设备绑定等方法，隐藏加密算法。
总的来说，没有不能抓取的app，只是时间成本的问题。

## 2.爬取思路
**1.抓包**
**2. HOOK**

### 2.1.抓包
有代码经验或APP开发的同学都很容易理解，其实很多APP，走的都是webservice通讯协议的方式，并且由于是公开数据，而且大部分是无加密的。所以只要对网络端口进行监测，对APP进行模拟操作，即可知道APP里面的数据是如何获取的。
我们只需要写代码模拟其请求，无论POST还是GET，即可得到该请求所返回的信息。再通过对返回的信息结构化解析，即可得到我们想要的数据。

```python
public static void main(String[] args) {
   Spider.create(new GithubRepoPageProcessor())
 
           //从https://github.com/****开始抓
 
           .addUrl("https://github.com/****")
 
           //设置Scheduler，使用Redis来管理URL队列
 
           .setScheduler(new RedisScheduler("localhost"))
 
           //设置Pipeline，将结果以json方式保存到文件
 
           .addPipeline(new JsonFilePipeline("D:\\data\\webmagic"))
 
           //开启5个线程同时执行
 
           .thread(5)
 
           //启动爬虫
 
           .run();
}
```
 

### 2.2.HOOK技术
HOOK技术是一种走操作系统内核的技术，由于安卓系统是开源的，所以可以借助一些框架修改内核，从而实现你要的功能。HOOK的形式，我们走的是Xposed框架。Xposed是一款可以在不修改任何其他开发者开发的应用（包括系统服务）的情况下，改变程序运行的一个开源框架服务。基于它可以制作出许多功能强大的模块，以此来达到应用程序按照你的意愿运行的目的。
如果把安卓手机看做一座城堡，那Xposed可以让你拥有一个上帝视角，城里的运作细节尽收你眼底，还能让你插一手改变城堡的运作规律。
什么意思呢？简单的说就是你可以通过他，自动化的控制你的APP。如果将我们的APP开在模拟器上，我们可以通过编码，通过他告诉APP这一步干什么，下一步干什么。你把它理解成类似按健精灵或游戏打怪外挂就可以了。
而他每走一步，APP与服务端交互的数据，均可获取下来。这种方式广泛用于一些成熟的APP。比如某信采集。
```python
public class HookActivity implements IXposedHookLoadPackage {
 
   @Override
 
   public void handleLoadPackage(LoadPackageParam lpparam) throws Throwable {
 
       final String packageName = lpparam.packageName;
 
       XposedBridge.log("--------------------: " + packageName);
 
       try {
 
           XposedBridge.hookAllMethods
 
           (Activity.class, "onCreate", new XC_MethodHook() {
 
               @Override
 
               protected void afterHookedMethod(MethodHookParam param)
 
               throws Throwable {
 
                   XposedBridge.log("=== Activity onCreate: " + param.thisObject);
 
               }
 
           });
 
       } catch (Throwable error) {
 
           XposedBridge.log("xxxxxxxxxxxx: " + error);
 
       }
 
   }
 
}
```

## 3.需要爬的坑

### **坑一：签名算法**
以某信的文章列表页及某信息页为例，对其http访问进行抓包，会发现其url的一个核心参数是我们无法知道如何生成的，这就导致，我们不可能直接用该url进行信息爬取；签名算法如果无法破解，HTTP这条路就是死路了。

### **坑二：http爬取回来的信息和页面显示不一致**
以某信的某信息页为例，对比直接访问某信页面及http爬取的信息，可明显发现http爬取到的信息较少。造成得两种方式都用，才能既照顾速度又照顾完整性。

### **坑三：模拟器中的坑**
APP自动识别你的运行环境进行屏蔽，最厉害的还是某信，连你是用模拟器打开还是真机打开，是什么内核的，全部进行限制。曾经见过牛人，找某手机厂商专门定做真机来配合。

### **坑四：帐号的坑**
这个坑就有点大了，要找号、养号，都不是件容易的事情，更惨的是封号，真真让你一夜回到解放前。

## 4.难度评估

### **1.一星**
此类app没有进行特殊的防护，可以直接在网页访问app中请求的url
困难点：无

### **2.二星**
此类app使用的cookie和session等技术，对数据的请求需要cookie等信息
困难点：
1、请求头需要附带cookie值

### **3.三星**
此类app在发起请求时，在headers中添加md5验证字段，该字段对请求的url的参数进行特殊的处理然后进行hash；如果想爬这类app，需要对app进行反向编译，经过大量的代码阅读，分析该app的hash算法和参数拼接；
困难点：
1、反编译
2、Android代码的阅读能力
3、花费大量时间和精力也不一定能找到，这是最蛋疼的。。。。

### **4.四星**
此类app对请求发起url请求，后台收到请求后在返回的数据中，针对有效数据进行加密，所以在用抓包工具进行分析时，无法看到具体的数据；如果想爬取这类app，只能先去反编译，然后分析出如何对请求数据加密的算法，只有完成了算法的破解才能进行数据的分析。
困难点：
1、无法通过抓包工具对所需数据进行分析
2、反编译
3、Android代码的阅读能力，寻找加密数据的算法
3、花费大量时间和精力也不一定能找到，这是最蛋疼的。。。。

## 5.工具

### **1. 抓包工具**
                   mac系统 Wireshark
                   Mac系统 charles
                   windows Fiddler

### **2.反编译**
Apktool，dex2jar，jd-gui-windows
Jadx-gui
能直接反编译dex文件，方便好用
JD-GUI
需要将dex文件转到jar文件，可以跳转函数
JEB
用的较少
**3.hook 工具**
Xposed
Frida




