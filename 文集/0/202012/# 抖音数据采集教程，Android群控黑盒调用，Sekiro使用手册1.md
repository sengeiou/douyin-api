# 抖音数据采集教程，Android群控黑盒调用，Sekiro使用手册


## 0x0 前言
之前尝试用过virjar大佬的hermesagent, 后来大佬又迭代出新的基于长链接的`Sekiro`, 一直想看都被耽搁了, 今天正好抽空尝试一下, 顺便写篇笔记, 有错误的地方大佬们请指正哈

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)  

## 0x1 Sekiro介绍
SEKIRO 是一个android下的API服务暴露框架，可以用在app逆向、app数据抓取、android群控等场景
 
**和其他群控框架相比的特点如下:**

- 对网络环境要求低，sekiro使用长链接管理服务，使得Android手机可以分布于全国各地，甚至全球各地。手机掺合在普通用户群体，方便实现反抓突破，更加适合获取下沉数据。
不依赖hook框架，就曾经的Hermes系统来说，和xposed框架深度集成，在当今hook框架遍地开花的环境下，框架无法方便迁移。所以在Sekiro的设计中，只提供了RPC功能了。

- 纯异步调用，在Hermes和其他曾经出现过的框架中，基本都是同步调用。虽然说签名计算可以达到上百QPS，但是如果用来做业务方法调用的话，由于调用过程穿透到目标app的服务器，会有大量请求占用线程。系统吞吐存在上线(hermes系统达到2000QPS的时候，基本无法横向扩容和性能优化了)。但是Sekiro全程使用NIO，理论上其吞吐可以把资源占满。

- client实时状态，在Hermes系统我使用http进行调用转发，通过手机上报心跳感知手机存活状态。心跳时间至少20s，这导致服务器调度层面对手机在线状态感知不及时，请求过大的时候大量转发调用由于client掉线timeout。在Sekiro长链接管理下，手机掉线可以实时感知。不再出现由于框架层面机制导致timeout


**Sekiro架构**
```python
server:
    暴露一个TCP端口和两个HTTP端口
    管理通过TCP连接的client和user发来的http 请求
client:
    通过TCP和server连接，响应server发来的请求
 
工作流程是这样的：
    1. client通过TCP和server建立长连接
    2. user发送http请求给server
    3. server根据用户发来的http请求的参数，通过TCP将请求转发给client
    4. client收到请求并响应server
    5. server将从client收到的请求返回给user
```
详细的可以去看项目的readme, 说的非常详细: [项目传送门](https://github.com/virjar/sekiro)

## 0x2 服务端部署

- 克隆项目: `git clone [https://github.com/virjar/sekiro.git](https://github.com/virjar/sekiro.git)`

- 修改settings.gradle的内容为: `include ':sekiro-server', ':sekiro-lib'` ,删掉appdemo防止编译它

- 启动服务器前注意事项

```python
server端在`sekiro-server/src/main/resources/appliation.properties`中可以配置三个服务端端口, 主要服务端安全策略的出入口需要开放这个三个端口
#tomcat 占用端口
server.port=5602
#长链接服务占用端口
natServerPort=5600
# 异步http占用端口
natHttpServerPort=5601
# websocket占用端口
webSocketServerPort=5603
 
如果自定义端口,client需要调用SekiroClient.start(String serverHost, int serverPort, final String clientID, String group)中去连接server
```
执行命令:`./gradlew sekiro-server:bootJar` 即可在 `sekiro-server/build/libs/sekiro-server-0.0.1-SNAPSHOT.jar`找到all-in-one的jar包


 
- 通过`nohup java -jar sekiro-server/build/libs/sekiro-server-0.0.1-SNAPSHOT.jar >/dev/null 2>&1 &`启动server
![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1609674939635-64619f96-f1e7-44c1-beac-b0cd0ea2d07f.jpeg#align=left&display=inline&height=674&originHeight=674&originWidth=1704&size=0&status=done&style=none&width=1704)

 

## 0x3 客户端

- 先准备一个测试的Demo, 很简单就是个加法, 注意Demo必须有加`android.permission.INTERNET`权限
```python
public class MainActivity extends AppCompatActivity {
 
  public static int Add(int n1, int n2)
  {
      return n1 + n2;
  }
 
  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_main);
 
      Toast.makeText(MainActivity.this, "3 + 2 = " + Add(3, 2), Toast.LENGTH_LONG).show();
  }
}
```

- 再准备的一个Xposed的项目, 不会的可以先看我之前写的一篇笔记: [AndroidStudio使用Xposed](http://strivemario.work/archives/34941.html)

- 在app的build.gradle添加依赖 `implementation 'com.virjar:sekiro-api:1.0.1'`



 
![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1609674939166-c6649d88-d877-467f-8989-ceaafcdb9c33.jpeg#align=left&display=inline&height=408&originHeight=408&originWidth=1150&size=0&status=done&style=none&width=1150)

 

- 在Xposed的`handleLoadPackage`中启动client链接server，并添加处理事件的handler, 用于调用Add函数
```python
Log.i(TAG, "connect server....");
//服务端host
String testHost = "your_host";
//客户端标识
String clientId = UUID.randomUUID().toString();
//接口组名称
String groupName = "addDemoTest2";
//暴露的接口名称
String actionName = "myAdd";
//拿classloader
clzLoader = lpparam.classLoader;
 
//连接服务端并且注册处理的handler
SekiroClient.start(testHost, clientId, groupName)
        .registerHandler(actionName, new SekiroRequestHandler(){
            @Override
            public void handleRequest(SekiroRequest sekiroRequest, SekiroResponse sekiroResponse){
 
                //当服务端分配任务时, 这里处理逻辑, 并把结果返回给服务端, 服务端再返回给调用者
                Class<?> clz = XposedHelpers.findClass("com.example.administrator.adddemo.MainActivity", clzLoader);
                int arg1  = sekiroRequest.getInt("arg1");
                int arg2  = sekiroRequest.getInt("arg2");
                Log.i(TAG, String.format("arg1 : %d, arg2 : %d", arg1, arg2));
                Object result = XposedHelpers.callStaticMethod(clz, "Add", arg1, arg2);
                Log.i(TAG, "result : " + result);
                sekiroResponse.success(result);
            }
 
        });
```

- 编译错误`More than one file was found with OS independent path`解决

```python
//build.gradle里android{}添加错误的path
 
packagingOptions {
    exclude 'META-INF/DEPENDENCIES'
    exclude 'META-INF/INDEX.LIST'
    exclude ('META-INF/io.netty.versions.properties')
}
```

## 0x4 尝试调用

- 打开app后, 使用浏览器查看group列表是否注册成功:
`[your_server_ip]:[server.port]/groupList`


 
![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1609674939382-668f646f-c4a2-480c-9763-8bca362f229b.jpeg#align=left&display=inline&height=480&originHeight=480&originWidth=1394&size=0&status=done&style=none&width=1394)

 

- 调用接口查看结果:
```python
格式: your_server_ip:[natHttpServerPort]/[invoke_type]?group=[group_id]&action=[action_name]&param1=[arg]
 
例子: https://x.x.x.x:5602/asyncInvoke?group=addDemoTest2&action=myAdd&arg1=300&arg2=300
```

 
![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1609674939370-a4a10414-f8e1-4b74-a7f6-e026e1432ef4.jpeg#align=left&display=inline&height=472&originHeight=472&originWidth=1754&size=0&status=done&style=none&width=1754)

 

- client的调用日志


 
![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1609674939371-fac1384e-a6d3-4952-af96-eddf3ef8b4f2.jpeg#align=left&display=inline&height=382&originHeight=382&originWidth=1600&size=0&status=done&style=none&width=1600)

 


## 感谢&参考资料
[sekiro实践](https://www.jianshu.com/p/6b71106c45eb?from=timeline)




___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
