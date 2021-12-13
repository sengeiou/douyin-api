# 抖音爬虫教程，AndServer+Service 打造 Android 服务器实现 so 文件调用


### so 文件调用
随着 Android 移动安全的高速发展，不管是为了执行效率还是程序的安全性等，关键代码下沉 native 层已成为基本操作。

native 层的开发就是通指的 JNI/NDK 开发，通过 JNI 可以实现 java 层和 native 层（主要是 C/C++ ）的相互调用，native 层经编译后产生 so 动态链接库，so 文件具有可移植性广，执行效率高，保密性强等优点。

那么问题来了，如何调用 so 文件显得异常重要，当然你也可以直接分析 so 文件的伪代码，利用强悍的编程功底直接模拟关键操作，但是我想对于普通人来说头发还是比较重要的。 

当前调用 so 文件的主流操作应该是： 
1，基于 Unicorn 的各种实现（还在学习中，暂且不表）  
2，Android 服务器的搭建，在 App 内起 http 服务完成调用 so 的需求（当然前提是过了 so 的效验等操作）

至于为什么选用 AndServer，好吧，不为什么，只是因为搜索到了它  
为什么结合 Service，在学习 Android 开发的时候了解到了 Service 的生命周期，个人理解用 Service 去创建 Http 服务比较好。 

当然也有 Application 的简单使用，因为在正式环境中，大多数 so 文件的逻辑中都有 context 的一些包名了，签名了的效验等，自定义 Application 的话获取 context 传参就好了。

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 
### libyemu.so 简介
这是我编译好的一个 so 文件，就是根据入参做下简单的字符串拼接（以下是 native 层编译前的 c 代码）
```
extern "C"
JNIEXPORT jstring JNICALL
Java_com_fw_myapplication_ndktest_NdkTest_stringFromUTF(JNIEnv *env, jobject instance, jstring str_) {
    jclass String_clazz = env->FindClass("java/lang/String");
    jmethodID concat_methodID = env->GetMethodID(String_clazz, "concat", "(Ljava/lang/String;)Ljava/lang/String;");
    jstring str = env->NewStringUTF("  from so --[NightTeam夜幕]");
    jobject str1 = env->CallObjectMethod(str_, concat_methodID, str);
    const char *chars = env->GetStringUTFChars((jstring)str1, 0);
    return env->NewStringUTF(chars);
}
```
这部分代码还是有必要贴一下的，简单的静态注册使用了反射的思想，反射在逆向中至关重要
接下来是 java 代码，定义了 native 函数
```
package com.fw.myapplication.ndktest;
public class NdkTest {
    public static native String stringFromUTF(String str);
    static {
        System.loadLibrary("yemu");
    }
}
```
如果到这里有点懵逼的同学可能需要去补下 Android 开发基础了

### Android 项目测试 so

先说下我的环境，因为这个环境影响太大了  
1，AndroidStudio 3.4  
2，手机 Android 6 架构 armeabi-v7a  
打开 AndroidStudio 新建 project  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407035-f0bfc349-9792-4b75-be4b-0b2c52f2cabc.webp#align=left&display=inline&height=669&originHeight=669&originWidth=916&size=0&status=done&style=none&width=916)
在 module 的 build 中加这么一句，然后 sync  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407052-e9d13adf-e910-4afb-a665-b3053bc311f4.webp#align=left&display=inline&height=487&originHeight=487&originWidth=1080&size=0&status=done&style=none&width=1080)
把编译好的 so 文件复制到 libs 文件夹下（和刚才的 jniLibs.srcDirs 对应）  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407005-a01d1eae-5a9c-4dea-b3ac-33b952d84bb0.webp#align=left&display=inline&height=653&originHeight=653&originWidth=394&size=0&status=done&style=none&width=394)
把 so 对应的 java 代码也 copy 过来，注意包名类名的一致性  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407082-db2d8716-c098-4ed4-bd8c-fedd76ab5fe3.webp#align=left&display=inline&height=706&originHeight=706&originWidth=1004&size=0&status=done&style=none&width=1004)
打开 activity_main.xml 文件为 TextView 添加 id  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407024-77218572-3108-47f8-ae6e-725f134855ea.webp#align=left&display=inline&height=730&originHeight=730&originWidth=1071&size=0&status=done&style=none&width=1071)
打开 MainActiviy.java 开始编码  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407075-f761c7b0-4c10-4a60-8208-0891932710b4.webp#align=left&display=inline&height=646&originHeight=646&originWidth=1080&size=0&status=done&style=none&width=1080)
这两行的意思就是，先从布局中找到对应 id 的 TextView，然后为其设置 Text（调用 native 函数的返回值）  
下面测试一下咱们的 so 调用情况  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407032-68a37c8c-0618-412a-9870-7a0513861383.webp#align=left&display=inline&height=598&originHeight=598&originWidth=349&size=0&status=done&style=none&width=349)
可以看到咱们的 so 文件调用成功（这里咱们的 so 没有效验，只是测试 app 是否可以正常调用）




### AndServer 代码编写

AndServer 官方文档：[https://yanzhenjie.com/AndServer/](https://yanzhenjie.com/AndServer/)  
打开官方文档，看看人家的入门介绍，新建 java 文件  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407079-c546568a-797d-45bb-92b8-8721d43f7b9c.webp#align=left&display=inline&height=602&originHeight=602&originWidth=1035&size=0&status=done&style=none&width=1035)
如图经典 MVC 的 C 就写好了，定义了一个 nightteam_sign 接口，请求方式为 get，请求参数为 sign，调用 native 函数，然后返回 json，但是这里我想利用 Application 获取下 context 对象，取下包名，接下来自定义 Applictaion



```
package com.nightteam.httpso;
import android.app.Application;
public class MyApp extends Application {
    private static MyApp myApp;
    public static MyApp getInstance() {
        return myApp;
    }
    @Override
    public void onCreate() {
        super.onCreate();
        myApp = this;
    }
}
```

然后在 manifest 文件中指定要启动的 Application  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407098-bc000c06-8516-4cbf-9acb-f57688e77c9f.webp#align=left&display=inline&height=380&originHeight=380&originWidth=698&size=0&status=done&style=none&width=698)
然后修改 MyController.java 的代码  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407145-8e1a9eea-97d6-4590-8907-8f398dfe636d.webp#align=left&display=inline&height=517&originHeight=517&originWidth=863&size=0&status=done&style=none&width=863)
接下来把官方文档-服务器的代码 copy 下来  
导入一些包，修改部分代码如下  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407080-6845caef-e74f-4668-8449-cfd3bce7183a.webp#align=left&display=inline&height=618&originHeight=618&originWidth=1080&size=0&status=done&style=none&width=1080)
新版本的 AndServer.serverBuilder 已经需要传递 context 了，这里把网络地址和端口号也修改为从构造参数中获取，到这里 AndServer 的东西基本完了，实际上咱们就搭建一个调 so 的接口，并没有过多的业务逻辑，所以代码就是使用的最简单的




### Service 代码编写

咱们这里用按钮的点击事件启动 Service，故在 activity_main.xml 中添加一个 button 并指定点击事件  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407141-4a7cd1c5-cbae-45b8-8ab8-ccebc5c14237.webp#align=left&display=inline&height=599&originHeight=599&originWidth=851&size=0&status=done&style=none&width=851)
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407048-3592a980-674e-482a-8e7e-4d5273e27539.webp#align=left&display=inline&height=364&originHeight=364&originWidth=728&size=0&status=done&style=none&width=728)
接下来编写自定义 Service 代码



```
package com.nightteam.httpso.Service;
import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
import android.util.Log;
import com.nightteam.httpso.ServerManager;
import java.net.InetAddress;
import java.net.UnknownHostException;
public class MyService extends Service {
    private static final String TAG = "NigthTeam";
    @Override
    public void onCreate() {
        super.onCreate();
        Log.d(TAG, "onCreate: MyService");
        new Thread() {
            @Override
            public void run() {
                super.run();
                InetAddress inetAddress = null;
                try {
                    inetAddress = InetAddress.getByName("0.0.0.0");
                    Log.d(TAG, "onCreate: " + inetAddress.getHostAddress());
                    ServerManager serverManager = new ServerManager(getApplicationContext(), inetAddress, 8005);
                    serverManager.startServer();
                } catch (UnknownHostException e) {
                    e.printStackTrace();
                }
            }
        }.start();
    }
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }
}
```

打上了几个 log，在子线程中启动 AndServer 的服务（何时使用 UI 线程和子线程是 Android 基础，这里就不赘述了）  
注意一下，这里从 0.0.0.0 获取 inetAddress，可不要写错了，localhost 和 0.0.0.0 的区别请移步搜索引擎  
然后就是向 ServerManager 的构造函数传递 context，inetAddress，port 用来 new 对象，随后开启服务
最后注意检查下 manifest 文件中 Service 的声明  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407052-31c60ff6-b22e-450d-8e5c-c1c09262e6f5.webp#align=left&display=inline&height=478&originHeight=478&originWidth=1080&size=0&status=done&style=none&width=1080)


### 开启 Service，并获取本机 ip
回到我们的 MainActivity.java 的 operate（ button 的点击事件）编写启动 Service 代码
```
public void operate(View view) {
        switch (view.getId()){
            case R.id.id_bt_index:
                //启动服务:创建-->启动-->销毁
                //如果服务已经创建了，后续重复启动，操作的都是同一个服务，不会再重新创建了，除非你先销毁它
                Intent it1 = new Intent(this, MyService.class);
                Log.d(TAG, "operate: button");
                startService(it1);
                ((Button) view).setText("服务已开启");
                break;
        }
    }
```
到这里我们的服务基本搭建好了，但是为了方便起见，我想把咱们的本机 ip 显示在 App 上，这样我们就不用去设置再查看了  
我在网上找到了一个获取 ip 地址的一个工具类，源码如下:
```
package com.nightteam.httpso;
import java.net.InetAddress;
import java.net.NetworkInterface;
import java.net.SocketException;
import java.util.Enumeration;
import java.util.regex.Pattern;
public class NetUtils {
    private static final Pattern IPV4_PATTERN = Pattern.compile("^(" +
            "([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\\.){3}" +
            "([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])$");
    private static boolean isIPv4Address(String input) {
        return IPV4_PATTERN.matcher(input).matches();
    }
    //获取本机IP地址
    public static InetAddress getLocalIPAddress() {
        Enumeration<NetworkInterface> enumeration = null;
        try {
            enumeration = NetworkInterface.getNetworkInterfaces();
        } catch (SocketException e) {
            e.printStackTrace();
        }
        if (enumeration != null) {
            while (enumeration.hasMoreElements()) {
                NetworkInterface nif = enumeration.nextElement();
                Enumeration<InetAddress> inetAddresses = nif.getInetAddresses();
                if (inetAddresses != null)
                    while (inetAddresses.hasMoreElements()) {
                        InetAddress inetAddress = inetAddresses.nextElement();
                        if (!inetAddress.isLoopbackAddress() && isIPv4Address(inetAddress.getHostAddress())) {
                            return inetAddress;
                        }
                    }
            }
        }
        return null;
    }
}
```

把工具类 copy 到我们的 Android 项目中，继续在 MainActivity.java 中编码  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407163-d26cdea2-8ce5-4203-a3c4-25310e766b6b.webp#align=left&display=inline&height=695&originHeight=695&originWidth=884&size=0&status=done&style=none&width=884)
获取了一下本机地址和 Android SDK 版本（ Android 8 之后启动 Service 方式不一样）



### 申请权限，启动 App

最后一步就是为 app 申请网络权限了  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407110-cb9b3296-138a-4863-82f7-eb06f5c42744.webp#align=left&display=inline&height=539&originHeight=539&originWidth=1080&size=0&status=done&style=none&width=1080)
随后连接我们的手机，运行项目，测试一下，点击开启服务  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407092-ac5136d3-2a91-4928-a8c2-387467faa419.webp#align=left&display=inline&height=591&originHeight=591&originWidth=348&size=0&status=done&style=none&width=348)
看下 AndroidStudio 日志  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407163-d33a6f38-6ce7-423a-813f-b86d9476b4e6.webp#align=left&display=inline&height=106&originHeight=106&originWidth=552&size=0&status=done&style=none&width=552)
好像一切正常，在浏览器访问下试试（ ip 就是 App 中显示的 ip 地址）  
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407048-18293989-fcf9-4ac7-8a5c-e72c95169a02.webp#align=left&display=inline&height=224&originHeight=224&originWidth=768&size=0&status=done&style=none&width=768)
如图正常访问到了我们想要的内容
回过头来说下 Service，打开我们手机的设置，找到应用程序管理-运行中的服务（手机不同，方式不同）
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407074-29e3e233-a167-429b-97a7-27fecc29fa1a.webp#align=left&display=inline&height=581&originHeight=581&originWidth=345&size=0&status=done&style=none&width=345)
可以看到我们的程序，运行了一个服务，这个服务就是咱们编码的 MyService
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407093-d4f37414-e08a-4bc0-b2e6-3b93565a0ae3.webp#align=left&display=inline&height=597&originHeight=597&originWidth=342&size=0&status=done&style=none&width=342)
接下来杀掉该 App进程，再次查看运行中的服务
![](https://cdn.nlark.com/yuque/0/2020/webp/97322/1607479407129-1d392cef-7b52-4d0a-83b7-32a922ee256d.webp#align=left&display=inline&height=613&originHeight=613&originWidth=347&size=0&status=done&style=none&width=347)
我这里在权限管理设置了自动运行，可以保持服务的运行。（这个地方还是根据系统有大小差异）
至此使用 App 起 http 服务调 so 就完成了

