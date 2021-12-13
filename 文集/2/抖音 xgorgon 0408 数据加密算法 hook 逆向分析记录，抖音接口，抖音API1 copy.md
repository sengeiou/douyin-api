# 抖音 xgorgon 0408 数据加密算法 hook 逆向分析记录，抖音接口，抖音API


#### 抖音xgorgon算法用ollvm混淆了，主要是流程平坦化，流程混淆和运算替换。

#### X-Gorgon是对cookie,X-SS-STUB,X-Khronos,Url进行混合加密之后的参数。这里也区分情况，有些接口只有url和X-Khronos参与接口加密，有些是url，X-Khronos，X-SS-STUB参与接口加密，有些则是所有都进行接口加密。

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)  

# 概述
抖音版本里面加了好几个算法，有as,cp(早期就这两个），mas,X-Gorgon，X-SS-STUB，X-Khronos算法，很多关键key之间有相互关联，只要有一个环节算错了，就会请求不到数据。目前版本的抖音加了很多的验证，及代码混淆，难度偏大。

# 初探
抖音的签名算法在libcms.so中，在JNI_Onload中动态注册jni函数。
算法用ollvm混淆了，主要是流程平坦化，流程混淆和运算替换
主要用到一些逆向工具IDA,Xposed框架

# 研究
8.0版本之后的算法主要是X-Gorgon和X-SS-STUB.之后经过抓包抖音接口，查看Java层,so层代码，分析如下原理。

- X-SS-STUB是post请求时body部分的md5值，但是在为空的情况下，有时候不参与加密，有时候参与加密，具体接口需要具体分析

- X-Khronos比较简单就是一个unix时间戳

- X-Gorgon是对cookie,X-SS-STUB,X-Khronos,Url进行混合加密之后的参数。这里也区分情况，有些接口只有url和X-Khronos参与接口加密，有些是url，X-Khronos，X-SS-STUB参与接口加密，有些则是所有都进行接口加密。具体接口具体分析


# **分析**
今天有空分享一下抖音的加密算法，作为拥有庞大用户量的APP，其通信协议加密的强度肯定是不弱的，关键算法被VM，只能动态分析去理解。我们通过抓包分析，请求的URL上带有AS、CP两个加密字段，这两个字段是早期版本算法，后又陆续添加了MAS、X-GORGON算法。我们今天先对AS、CP两个字段进行分析，这个只能通过动态调试去跟踪加密过程。
首先我们通过工具调试定位到函数
```
- [IESAntiSpam testForAlert:msg:]
```
定位的详细过程忽略……，进入继续调试后发现调用SUB_102E3345函数进行加密排序
1.整理分析流程


```
 
1.时间戳转十六进制
 
2.将时间戳排序俩次，
  a1 v3 是排序key
  sprintf(byte_102323F30, "%08x", a1);
  sprintf(byte_102323F3A, "%08x", v3);
 
3.将url参数用MD5加密一次或俩次根据时间戳&运算
 
4.将第一次排序结果写入前16位地址加一写入（从1插入），隔一位插入，前边拼a1
 
5.将第二次排序结果写入后16位（从0插入）后边拼e1
 
```
2.结果排序
```
a1d5b43se234dccea7
 
456dcd5s2320cf3e1
 
&cp=456fcd5s2320cfs3e1&as=a1d5b43se234dccea7
 
拼接完成后就可以请求了
```
后期版本添加了mas算法和最新的X-gorgon算法，目前最新系列版本算法如果需要了解的话可以交流。
3.流程详述


 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1606705772468-a9630ae1-df91-45b8-8905-e6ac160a3cac.png#align=left&display=inline&height=760&name=image.png&originHeight=1520&originWidth=2294&size=779439&status=done&style=none&width=1147)

 

那么我们就能够更加确信header里的x-gorgon对它进行了一次签名，所以我们直接jadx上手阅读一波反编译后的代码，这里我直接搜索了x-gorgon关键字，列出了以下结果：
那么我们就能够更加确信header里的x-gorgon对它进行了一次签名，所以我们直接jadx上手阅读一波反编译后的代码，这里我直接搜索了x-gorgon关键字，列出了以下结果：

 
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606705695209-9d6f9a14-5bf0-49a4-ba2a-811bd7d074f4.png#align=left&display=inline&height=271&originHeight=271&originWidth=894&size=0&status=done&style=none&width=894)

 
这里我选择了hashMap.put("X-Gorgon", a3);这一行，跳转进去我们来分析一下它的代码
        这里我们看到有一个它的值是来自a3，a3则是通过String a3 = a.a(com.ss.sys.ces.a.leviathan(i, currentTimeMillis, a.a(a2 + str4 + str5 + str6)));这行代码进行获取到的结果，我们看到它传了4个参数，我们来仔细看一下这4个参数具体都是什么内容：
        **a2来源：**
            String b2 = tt.d(str);
            d.a(b2);
        str它就是该方法传进来的参数，我们后面可以通过hook方式来获取它的具体内容，而它会执行tt.d()、d.a() 进行2次操作，我们对其tt.d()跟进去

 
        ![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1606705797689-f87d26a0-79be-4c0f-8db6-b6315ab2176d.png#align=left&display=inline&height=230&name=image.png&originHeight=460&originWidth=758&size=84473&status=done&style=none&width=379)

 


        我们看到它对这个字符串进行了取 ? 和 # 中间值，怀疑是url，如果是url证明它只是取了url后面的参数，那么继续看它的下一个方法：d.a() 
        我们看到这里就是进行了MD5签名取值，那么a2分析到此结束，我们继续分析第2个参数

 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1606705812012-7fe5194f-b053-4f17-b9c8-bbaf45db0ace.png#align=left&display=inline&height=201&name=image.png&originHeight=402&originWidth=872&size=90358&status=done&style=none&width=436)

 


        **str4来源： **
**      **这里非常简单，它就是枚举传进来的第二个参数map，判断如果有X-SS-STUB这个值的话就获取，反之则填充32个0，那么我们抓包发现并没有X-SS-STUB这个参数，实际上如果我们的包是POST的话它就会有，实际上它就是POST数据的一个MD5签名值。
        **str5来源：**
str5也非常简单，也是枚举map里面有没有COOKIE，如果有就把COOKIE进行MD5，那么该参数也到此结束了
        **str6来源：**
```
String c2 = tt.e(str3);
if (c2 != null && c2.length() > 0) {
     str6 = d.a(c2);
     StcSDKFactory.getInstance().setSession(c2);
}
```
        这里我们记得str3是cookie，它执行了tt.e(str3) 方法获取一个返回值，如果它不是空同样给这个返回值md5，那么我们跟进去看一下它是做了什么处理：
        这里我们看到它是枚举了cookie里面有没有sessionid这个值，如果有就取出来，那么str6到此结束
        **参数整理：**
            a2 = md5(url) 疑似对网址的参数进行md5
            str4 = x-ss-stub，只有post时才有效，否则是32个0
            str5 = md5(cookie)  对cookie进行md5
            str6 = md5(cookie['sessionid'])    对cookie里面对sessionid进行md5，否则也是32个0
        我们整理完了这4条参数后，继续分析，它将这4个参数进行了字符串合并，接着执行 a.a(a2+str4+str5+str6)，我们跟进去看看里面做了什么操作
        我们看到它这里循环了总长度/2次，每次都是把  str[i] 转换成十进制左移4，然后加上 str[i+1] 都一个简单运算，并返回结果，也就是本来是4个32位（128位）然后经过加密后缩短成了64位长度。最后它执行了com.ss.sys.ces.a.leviathan(i, currentTimeMillis, a.a(a2 + str4 + str5 + str6))进行计算，我们看到它还传了2个参数，i和currentTimeMillis，我们往前可以看到 i是-1，而currentTimeMillis是当前都十位时间戳。

 
    ![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1606705868031-971a40d5-76cc-44b1-9f5c-d3e124d2e0a1.png#align=left&display=inline&height=209&name=image.png&originHeight=418&originWidth=908&size=96354&status=done&style=none&width=454)

 
        最后把计算好都byteArray经过位移转换成了string类型，并put到map里面，那么我们也清楚到看到，k-khronos也就是刚刚到currentTimeMillis时间戳了。我们发现由于om.ss.sys.ces.a.leviathan是在so层到libcms.so文件，并且里面有大量到混淆就没有再度分析。我们可以通过xposed或者unidbg到方法进行调用。

 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1606705897710-915c991b-108b-4826-b1e8-a2008415359a.png#align=left&display=inline&height=482&name=image.png&originHeight=964&originWidth=2134&size=182979&status=done&style=none&width=1067)

 

## **0x02：参数确认**
** **       这里我们分析完了它算法到具体参数构造完成后，我们还需要确认它传到参数是否是我们所联想到，那么这里我们发现由于它这个方法是一个callback，我们往前跟一下，寻找一个合适到hook点，使用frida进行hook

 
        ![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1606705914986-29a9737a-fca4-41be-a3cc-35b10a2b1576.png#align=left&display=inline&height=206&name=image.png&originHeight=412&originWidth=2126&size=116829&status=done&style=none&width=1063)

 
        这里我对它进行了调用查找，看到只有1个地方，我们跟进去看看，跟进去之后它就是以下内容，就只是一个简单对赋值给sAddSecurityFactorProcessCallback，我们在对它进行调用查找，看看是什么地方对它进行对调用。
>  public static void setAddSecurityFactorProcessCallback(a aVar) {
>         sAddSecurityFactorProcessCallback = aVar;
>     }    
>  

 
    这里我们看到它从这里取的回调指针变量，然后判断如果不为null则执行，那么我们就可以直接hook这个方法：tryAddSecurityFactor$___twin___，这里我的hook代码也就比较简单，直接输出它传进去的map和str的值以及返回的map进行确认。
```
//frida -U com.ss.android.ugc.aweme -l test.js
Java.perform(function() {
    var NetworkParams = Java.use("com.bytedance.frameworks.baselib.network.http.NetworkParams");
    NetworkParams['tryAddSecurityFactor$___twin___'].implementation = function(str,map){
    var keyset = map.keySet();
    var it = keyset.iterator();
    console.log("str:\t"+str)
    while(it.hasNext()){
        var keystr = it.next().toString();
        var valuestr = map.get(keystr).toString()
        console.log("map:\t"+keystr+"\t"+valuestr)
    }
    var ret
    ret = this.tryAddSecurityFactor$___twin___(str,map);
    var keyset = ret.keySet();
    var it = keyset.iterator();
    while(it.hasNext()){
        var keystr = it.next().toString();
        var valuestr = ret.get(keystr).toString()
        console.log("ret map:\t"+keystr+"\t"+valuestr)
    }
    return ret;
    }
});
```
>  

绿色部分就是str参数1的值，黄色则是map，蓝色则是返回的map，我们看下charles的这个包的header里的xgorgon是不是返回的值。

 
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606705695346-e412c012-7580-4df1-813b-45206808375c.png#align=left&display=inline&height=650&originHeight=650&originWidth=1434&size=0&status=done&style=none&width=1434)

 

 
![](https://cdn.nlark.com/yuque/0/2020/png/97322/1606705695985-2900b1c5-ea73-4dd2-b47f-6cb38ceafca9.png#align=left&display=inline&height=650&originHeight=650&originWidth=1434&size=0&status=done&style=none&width=1434)

 

## 3.总结：
以上就是对抖音对一个简单的x-gorgon的分析笔记过程，希望能够有所帮助，也能够对自身的产品安全方面进行一个参考借鉴。





___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
