# 抖音数据采集Frida教程，Java、Interceptor、NativePointer(Function/Callback)使用方法及示例

注意，运行以下任何代码时都需要提前启动手机中的`frida-server`文件。
 

## 1.1 Java对象
`Java`是十分哦不，应该说是极其重要的`API`，无论是想对`so`层亦或java层进行拦截，都必须编写`Java.perform`，在使用上面这些`API`时，应该都已经发现了吧~这章我们就来详细看看`Java`对象都有哪些`API`~

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

### 1.1.1 Java.available
该函数一般用来判断当前进程是否加载了`JavaVM，Dalvik`或`ART`虚拟机，咱们来看代码示例！
```python
function frida_Java() {
    Java.perform(function () {
        //作为判断用
        if(Java.available)
        {
            //注入的逻辑代码
            console.log("hello java vm");
        }else{
            //未能正常加载JAVA VM
            console.log("error");
        }
    });
}       
setImmediate(frida_Java,0);
输出如下。
hello java vm
```
核心注入的逻辑代码写在<注入的逻辑代码>内会非常的安全万无一失~

### 1.1.2 Java.androidVersion
显示android系统版本号
```python
function frida_Java() {
    Java.perform(function () {
        //作为判断用
        if(Java.available)
        {
            //注入的逻辑代码
            console.log("",Java.androidVersion);
        }else{
            //未能正常加载JAVA VM
            console.log("error");
        }
    });
}       
setImmediate(frida_Java,0);
输出如下。
9
因为我的系统版本是9版本~
```

### 1.1.3 枚举类Java.enumerateLoadedClasses
该API枚举当前加载的所有类信息，它有一个回调函数分别是`onMatch、onComplete`函数，我们来看看代码示例以及效果！
```python
function frida_Java() {
    Java.perform(function () {
        if(Java.available)
        {
            //console.log("",Java.androidVersion);
            //枚举当前加载的所有类
            Java.enumerateLoadedClasses({
                //每一次回调此函数时其参数className就是类的信息
                onMatch: function (className)
                {
                    //输出类字符串
                    console.log("",className);
                },
                //枚举完毕所有类之后的回调函数
                onComplete: function ()
                {
                    //输出类字符串
                    console.log("输出完毕");
                }
            });
        }else{
            console.log("error");
        }
    });
}       
setImmediate(frida_Java,0);
```

 
咱们来看执行的效果图1-7。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1610978198228-40c5b653-a483-4f9a-a5cb-2a76d0844071.png#align=left&display=inline&height=612&name=image.png&originHeight=1224&originWidth=2578&size=1399233&status=done&style=none&width=1289)
图1-7 终端执行
它还有一个好兄弟 `Java.enumerateLoadedClassesSync()`，它返回的是一个数组。

 

### 1.1.4 枚举类加载器Java.enumerateLoadedClasses
该`api`枚举`Java VM`中存在的类加载器，其有一个回调函数，分别是`onMatch: function (loader)`与`onComplete: function ()`，接着我们来看代码示例。
```python
function frida_Java() {
    Java.perform(function () {
        if(Java.available)
        {
            //枚举当前加载的Java VM类加载器
            Java.enumerateClassLoaders({
                //回调函数，参数loader是类加载的信息
                onMatch: function (loader)
                {
                    console.log("",loader);
                },
                //枚举完毕所有类加载器之后的回调函数
                onComplete: function ()
                {
                    console.log("end");
                }
            });
        }else{
            console.log("error");
        }
    });
}       
setImmediate(frida_Java,0);
```

 
执行的效果图1-8。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1610978226343-e9e22e30-4127-49c8-977d-5019c94f7599.png#align=left&display=inline&height=279&name=image.png&originHeight=558&originWidth=2730&size=628890&status=done&style=none&width=1365)
图1-8 终端执行
它也有一个好兄弟叫`Java.enumerateClassLoadersSync()`也是返回的数组。

 

### 1.1.5 附加调用Java.perform
该`API`极其重要，`Java.perform（fn）`主要用于当前线程附加到`Java VM`并且调用`fn`方法。我们来看看示例代码及其含义。
```python
function frida_Java() {
    //运行当前js脚本时会对当前线程附加到Java VM虚拟机，并且执行function方法
    Java.perform(function () {
        //判断是否Java VM正常运行
        if(Java.available)
        {
            //如不意外会直接输出 hello
            console.log("hello");
        }else{
            console.log("error");
        }
    });
}       
setImmediate(frida_Java,0);
输出如下。
[Google Pixel::com.roysue.roysueapplication]-> hello
```
没错你猜对了，它也有一个好兄弟。`Java.performNow(fn)~`

### 1.1.6 获取类Java.use
`Java.use(className)，`动态获取`className`的类定义，通过对其调用`$new()`来调用构造函数，可以从中实例化对象。当想要回收类时可以调用`$Dispose()`方法显式释放，当然也可以等待`JavaScript`的垃圾回收机制，当实例化一个对象之后，可以通过其实例对象调用类中的静态或非静态的方法，官方代码示例定义如下。
```python
Java.perform(function () {
  //获取android.app.Activity类
  var Activity = Java.use('android.app.Activity');
  //获取java.lang.Exception类
  var Exception = Java.use('java.lang.Exception');
  //拦截Activity类的onResume方法
  Activity.onResume.implementation = function () {
    //调用onResume方法的时候，会在此处被拦截并且调用以下代码抛出异常！
    throw Exception.$new('Oh noes!');
  };
});
```

### 1.1.7 扫描实例类Java.choose
在堆上查找实例化的对象，示例代码如下！
```python
Java.perform(function () {
    //查找android.view.View类在堆上的实例化对象
    Java.choose("android.view.View", {
        //枚举时调用
        onMatch:function(instance){
            //打印实例
            console.log(instance);
        },
        //枚举完成后调用
        onComplete:function() {
            console.log("end")
        }});
});
输出如下：
android.view.View{2292774 V.ED..... ......ID 0,1794-1080,1920 #1020030 android:id/navigationBarBackground}
android.view.View{d43549d V.ED..... ......ID 0,0-1080,63 #102002f android:id/statusBarBackground}
end
```

### 1.1.8 类型转换器Java.cast
`Java.cast(handle, klass)`，就是将指定变量或者数据强制转换成你所有需要的类型；创建一个 `JavaScript` 包装器，给定从 `Java.use（）` 返回的给定类`klas`的句柄的现有实例。此类包装器还具有用于获取其类的包装器的类属性，以及用于获取其类名的字符串表示的`$className`属性，通常在拦截`so`层时会使用此函数将`jstring、jarray`等等转换之后查看其值。

### 1.1.9 定义任意数组类型Java.array
`frida`提供了在js代码中定义`java`数组的`api`，该数组可以用于传递给`java API`，我们来看看如何定义，代码示例如下。
```python
Java.perform(function () {
        //定义一个int数组、值是1003, 1005, 1007
        var intarr = Java.array('int', [ 1003, 1005, 1007 ]);
        //定义一个byte数组、值是0x48, 0x65, 0x69
        var bytearr = Java.array('byte', [ 0x48, 0x65, 0x69 ]);
        for(var i=0;i<bytearr.length;i++)
        {
            //输出每个byte元素
            console.log(bytearr[i])
        }
});
```
我们通过上面定义`int`数组和`byte`的例子可以知道其定义格式为`Java.array('type',[value1,value2,....]);`那它都支持`type`呢？我们来看看~



| type | 含义 |
| --- | --- |
| Z | boolean |
| B | byte |
| C | char |
| S | short |
| I | int |
| J | long |
| F | float |
| D | double |
| V | void |


### 1.1.10 注册类Java.registerClass(spec)
`Java.registerClass`：创建一个新的`Java`类并返回一个包装器，其中规范是一个包含：
`name`：指定类名称的字符串。
`superClass`：（可选）父类。要从 `java.lang.Objec`t 继承的省略。
`implements`：（可选）由此类实现的接口数组。
`fields`：（可选）对象，指定要公开的每个字段的名称和类型。
`methods`：（可选）对象，指定要实现的方法。
注册一个类，返回类的实例，下面我贴一个基本的用法~实例化目标类对象并且调用类中的方法
```python
Java.perform(function () {
          //注册一个目标进程中的类，返回的是一个类对象
          var hellojni = Java.registerClass({
            name: 'com.roysue.roysueapplication.hellojni'
          });
          console.log(hellojni.addInt(1,2));
});
```
我们再深入看看官方怎么来玩的：
```python
//获取目标进程的SomeBaseClass类
var SomeBaseClass = Java.use('com.example.SomeBaseClass');
//获取目标进程的X509TrustManager类
var X509TrustManager = Java.use('javax.net.ssl.X509TrustManager');
var MyWeirdTrustManager = Java.registerClass({
  //注册一个类是进程中的MyWeirdTrustManager类
  name: 'com.example.MyWeirdTrustManager',
  //父类是SomeBaseClass类
  superClass: SomeBaseClass,
  //实现了MyWeirdTrustManager接口类
  implements: [X509TrustManager],
  //类中的属性
  fields: {
    description: 'java.lang.String',
    limit: 'int',
  },
  //定义的方法
  methods: {
    //类的构造函数
    $init: function () {
      console.log('Constructor called');
    },
    //X509TrustManager接口中方法之一，该方法作用是检查客户端的证书
    checkClientTrusted: function (chain, authType) {
      console.log('checkClientTrusted');
    },
    //该方法检查服务器的证书，不信任时。在这里通过自己实现该方法，可以使之信任我们指定的任何证书。在实现该方法时，也可以简单的不做任何处理，即一个空的函数体，由于不会抛出异常，它就会信任任何证书。
    checkServerTrusted: [{
      //返回值类型
      returnType: 'void',
      //参数列表
      argumentTypes: ['[Ljava.security.cert.X509Certificate;', 'java.lang.String'],
      //实现方法
      implementation: function (chain, authType) {
         //输出
        console.log('checkServerTrusted A');
      }
    }, {
      returnType: 'java.util.List',
      argumentTypes: ['[Ljava.security.cert.X509Certificate;', 'java.lang.String', 'java.lang.String'],
      implementation: function (chain, authType, host) {
        console.log('checkServerTrusted B');
        //返回null会信任所有证书
        return null;
      }
    }],
    //　返回受信任的X509证书数组。
    getAcceptedIssuers: function () {
      console.log('getAcceptedIssuers');
      return [];
    },
  }
});
```
我们来看看上面的示例都做了啥?实现了证书类的`javax.net.ssl.X509TrustManager`类，，这里就是相当于自己在目标进程中重新创建了一个类，实现了自己想要实现的类构造，重构造了其中的三个接口函数、从而绕过证书校验。

### 1.1.11 Java.vm对象
`Java.vm`对象十分常用，比如想要拿到`JNI`层的`JNIEnv`对象，可以使用`getEnv()`；我们来看看具体的使用和基本小实例。~
```python
function frida_Java() {     
    Java.perform(function () {
         //拦截getStr函数
         Interceptor.attach(Module.findExportByName("libhello.so" , "Java_com_roysue_roysueapplication_hellojni_getStr"), {
            onEnter: function(args) {
                console.log("getStr");
            },
            onLeave:function(retval){
                //它的返回值的是retval 在jni层getStr的返回值的jstring 
                //我们在这里做的事情就是替换掉结果
                //先获取一个Env对象
                var env = Java.vm.getEnv();
                //通过newStringUtf方法构建一个jstirng字符串
                var jstring = env.newStringUtf('roysue');
                //replace替换掉结果
                retval.replace(jstring);
                console.log("getSum方法返回值为:roysue")
            }
    });
}
setImmediate(frida_Java,0);
```
 

## 1.2 Interceptor对象
该对象功能十分强大，函数原型是`Interceptor.attach(target, callbacks)`:参数`target`是需要拦截的位置的函数地址，也就是填某个`so`层函数的地址即可对其拦截，`target`是一个`NativePointer`参数，用来指定你想要拦截的函数的地址，`NativePointer`我们也学过是一个指针。需要注意的是对于`Thumb`函数需要对函数地址`+1`，`callbacks`则是它的回调函数，分别是以下两个回调函数：

### 1.2.1 Interceptor.attach
`onEnter：`函数（`args`）：回调函数，给定一个参数`args`，可用于读取或写入参数作为 `NativePointer` 对象的数组。
`onLeave：`函数（`retval`）：回调函数给定一个参数 `retval`，该参数是包含原始返回值的 `NativePointer` 派生对象。可以调用 `retval.replace（1337）` 以整数 `1337` 替换返回值，或者调用 `retval.replace（ptr（"0x1234"））`以替换为指针。请注意，此对象在 `OnLeave` 调用中回收，因此不要将其存储在回调之外并使用它。如果需要存储包含的值，请制作深副本，例如：`ptr（retval.toString（））`。
我们来看看示例代码~
```python
//使用Module对象getExportByNameAPI直接获取libc.so中的导出函数read的地址，对read函数进行附加拦截
Interceptor.attach(Module.getExportByName('libc.so', 'read'), {
  //每次read函数调用的时候会执行onEnter回调函数
  onEnter: function (args) {
    this.fileDescriptor = args[0].toInt32();
  },
  //read函数执行完成之后会执行onLeave回调函数
  onLeave: function (retval) {
    if (retval.toInt32() > 0) {
      /* do something with this.fileDescriptor */
    }
  }
});
```
通过我们对`Interceptor.attach`函数有一些基本了解了~它还包含一些属性
我们来看看示例代码。
```
function frida_Interceptor() {
    Java.perform(function () {
        //对So层的导出函数getSum进行拦截
        Interceptor.attach(Module.findExportByName("libhello.so" , "Java_com_roysue_roysueapplication_hellojni_getSum"), {
            onEnter: function(args) {
                //输出
                console.log('Context information:');
                //输出上下文因其是一个Objection对象，需要它进行接送、转换才能正常看到值
                console.log('Context  : ' + JSON.stringify(this.context));
                //输出返回地址
                console.log('Return   : ' + this.returnAddress);
                //输出线程id
                console.log('ThreadId : ' + this.threadId);
                console.log('Depth    : ' + this.depth);
                console.log('Errornr  : ' + this.err);
            },
            onLeave:function(retval){
            }
        });
    });
}
setImmediate(frida_Interceptor,0);
```


 

我们注入脚本之后来看看执行之后的效果以及输出的这些都是啥，执行的效果图`1-9`。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1610978494585-97a9f749-30c1-4fc6-a159-e81d4b75a6a5.png#align=left&display=inline&height=330&name=image.png&originHeight=660&originWidth=1666&size=324308&status=done&style=none&width=833)
图1-9 终端执行

 

### 1.2.2 Interceptor.detachAll
简单来说这个的函数的作用就是让之前所有的`Interceptor.attach`附加拦截的回调函数失效。

### 1.2.3 Interceptor.replace
相当于替换掉原本的函数，用替换时的实现替换目标处的函数。如果想要完全或部分替换现有函数的实现，则通常使用此函数。，我们也看例子，例子是最直观的！代码如下。
```python
function frida_Interceptor() {
    Java.perform(function () {
       //这个c_getSum方法有两个int参数、返回结果为两个参数相加
       //这里用NativeFunction函数自己定义了一个c_getSum函数
       var add_method = new NativeFunction(Module.findExportByName('libhello.so', 'c_getSum'), 
       'int',['int','int']);
       //输出结果 那结果肯定就是 3
       console.log("result:",add_method(1,2));
       //这里对原函数的功能进行替换实现
       Interceptor.replace(add_method, new NativeCallback(function (a, b) {
           //h不论是什么参数都返回123
            return 123;
       }, 'int', ['int', 'int']));
       //再次调用 则返回123
       console.log("result:",add_method(1,2));
    });
}
```

 
我来看注入脚本之后的终端是是不是显示了`3`和`123`见下图`1-10`。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1610978514683-d619752f-be28-4892-838d-a18417c8dfbd.png#align=left&display=inline&height=246&name=image.png&originHeight=492&originWidth=1518&size=217886&status=done&style=none&width=759)
图1-10 终端执行
 

 

## 1.3 NativePointer对象
同等与C语言中的指针

### 1.3.1 new NativePointer(s)
声明定义NativePointer类型
```python
function frida_NativePointer() {
    Java.perform(function () {
        //第一种字符串定义方式 十进制的100 输出为十六进制0x64
        const ptr1 = new NativePointer("100");
        console.log("ptr1:",ptr1);
        //第二种字符串定义方式 直接定义0x64 同等与定义十六进制的64
        const ptr2 = new NativePointer("0x64");
        console.log("ptr2:",ptr2);        
        //第三种定数值义方式 定义数字int类型 十进制的100 是0x64
        const ptr3 = new NativePointer(100);
        console.log("ptr3:",ptr3);
    });
}     
setImmediate(frida_NativePointer,0);
输出如下，都会自动转为十六进制的0x64
ptr1: 0x64
ptr2: 0x64
ptr3: 0x64
```

### 1.3.2 运算符以及指针读写API

 
它也能调用以下运算符
[![](https://cdn.nlark.com/yuque/0/2021/png/97322/1610978069059-9f2dc103-b328-41f0-89e8-2a0f3f1d0d1c.png#align=left&display=inline&height=1199&originHeight=1199&originWidth=808&size=0&status=done&style=none&width=808)](https://p3.ssl.qhimg.com/t0135a7319c873d8d52.png)
[![](https://cdn.nlark.com/yuque/0/2021/png/97322/1610978069082-5703ad2c-877e-4228-a4d5-d1cef781a6dc.png#align=left&display=inline&height=693&originHeight=693&originWidth=811&size=0&status=done&style=none&width=811)](https://p3.ssl.qhimg.com/t01821e14d1331f0aef.png)
看完API含义之后，我们来使用他们，下面该脚本是readByteArray()示例~

 
```python
function frida_NativePointer() {
    Java.perform(function () {
       console.log("");
        //拿到libc.so在内存中的地址
        var pointer = Process.findModuleByName("libc.so").base;
        //读取从pointer地址开始的16个字节
        console.log(pointer.readByteArray(0x10));
    });
}     
setImmediate(frida_NativePointer,0);
输出如下：
           0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F  0123456789ABCDEF
00000000  7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00  .ELF............
```
首先我先来用`readByteArray`函数来读取`libc.so`文件在内存中的数据，这样我们方便测试，我们从`libc`文件读取`0x10`个字节的长度，肯定会是`7F 45 4C 46...`因为`ELF`文件头部信息中的`Magic`属性。

### 1.3.3 readPointer()
咱们直接从`API`索引11开始玩readPointer()，定义是从此内存位置读取`NativePointer`，示例代码如下。省略`function`以及`Java.perform`~
```python
var pointer = Process.findModuleByName("libc.so").base;
    console.log(pointer.readByteArray(0x10));
    console.log("readPointer():"+pointer.readPointer());
    输出如下。
    readPointer():0x464c457f
```
也就是将`readPointer`的前四个字节的内容转成地址产生一个新的`NativePointer`。

### 1.3.4 writePointer(ptr)
读取ptr指针地址到当前指针
```python
//先打印pointer指针地址
        console.log("pointer :"+pointer);
        //分配四个字节的空间地址
        const r = Memory.alloc(4);
        //将pointer指针写入刚刚申请的r内
        r.writePointer(pointer);
        //读取r指针的数据
        var buffer = Memory.readByteArray(r, 4);
        //r指针内放的pointer指针地址
        console.log(buffer);
输出如下。
//console.log("pointer :"+pointer); 这句打印的地址 也就是libc的地址
pointer :0xf588f000
//console.log(buffer); 输出buffer 0xf588f000在内存数据会以00 f0 88 f5方式显示
           0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F  0123456789ABCDEF
00000000  00 f0 88 f5                                      ....
```

### 1.3.5 readS32()、readU32()
从该内存位置读取有符号或无符号`8/16/32/etc`或浮点数/双精度值，并将其作为数字返回。这里拿`readS32()、readU32()`作为演示.
```python
//从pointer地址读4个字节 有符号
    console.log(pointer.readS32());
    //从pointer地址读4个字节 无符号
    console.log(pointer.readU32());
输出如下。
           0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F  0123456789ABCDEF
00000000  7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00  .ELF............
1179403647 == 0x464c457f
1179403647 == 0x464c457f
```

### 1.3.6 writeS32()、writeU32()
将有符号或无符号`8/16/32/`等或浮点数/双精度值写入此内存位置。
```python
//申请四个字节的内存空间
    const r = Memory.alloc(4);
    //将0x12345678写入r地址中
    r.writeS32(0x12345678);
    //输出
    console.log(r.readByteArray(0x10));
// writeS32()、writeU32()输出的也是一样的，只是区别是有符号和无符号
输出如下。
           0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F  0123456789ABCDEF
00000000  78 56 34 12 00 00 00 00 00 00 00 00 00 00 00 00  xV4.............
```

### 1.3.7 readByteArray(length))、writeByteArray(bytes)
`readByteArray(length))`连续读取内存`length`个字节，、`writeByteArray`连续写入内存`bytes`。
```python
//先定义一个需要写入的字节数组
       var arr = [ 0x72, 0x6F, 0x79, 0x73, 0x75, 0x65];
       //这里申请以arr大小的内存空间
       const r = Memory.alloc(arr.length);
       //将arr数组字节写入r
       Memory.writeByteArray(r,arr);
       //读取arr.length大小的数组
       var buffer = Memory.readByteArray(r, arr.length);
       console.log("Memory.readByteArray:");
       console.log(hexdump(buffer, {
            offset: 0,
            length: arr.length,
            header: true,
            ansi: false
        }));
输出如下。       
Memory.readByteArray:
           0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F  0123456789ABCDEF
00000000  72 6f 79 73 75 65                                roysue
```

### 1.3.8 readCString([size = -1])、writeUtf8String(str)
`readCString`功能是读取指针地址位置的字节字符串，对应的`writeUtf8String`是写入指针地址位置的字符串处。（这里的`r`是接着上面的代码的变量）。
```python
//在这里直接使用readCString读取会把上面的'roysue'字符串读取出来
        console.log("readCString():"+r.readCString());
        //这里是写入字符串 也就是 roysue起始位置开始被替换为haha
        const newPtrstr = r.writeUtf8String("haha");
        //替换完了之后再继续输出 必然是haha
        console.log("readCString():"+newPtrstr.readCString());
```


图1-11 终端执行
 

## 1.4 NativeFunction对象
创建新的`NativeFunction`以调用`address`处的函数(用`NativePointer`指定)，其中`rereturn Type`指定返回类型，`argTypes`数组指定参数类型。如果不是系统默认值，还可以选择指定`ABI`。对于可变函数，添加一个‘.’固定参数和可变参数之间的`argTypes`条目，我们来看看官方的例子。
```python
// LargeObject HandyClass::friendlyFunctionName();
//创建friendlyFunctionPtr地址的函数
var friendlyFunctionName = new NativeFunction(friendlyFunctionPtr,
    'void', ['pointer', 'pointer']);
//申请内存空间    
var returnValue = Memory.alloc(sizeOfLargeObject);
//调用friendlyFunctionName函数
friendlyFunctionName(returnValue, thisPtr);
```
我来看看它的格式，函数定义格式为`new NativeFunction(address, returnType, argTypes[, options])，`参照这个格式能够创建函数并且调用`！returnType和argTypes[，]`分别可以填`void、pointer、int、uint、long、ulong、char、uchar、float、double、int8、uint8、int16、uint16、int32、uint32、int64、uint64`这些类型，根据函数的所需要的type来定义即可。
在定义的时候必须要将参数类型个数和参数类型以及返回值完全匹配，假设有三个参数都是`int`，则`new NativeFunction(address, returnType, ['int', 'int', 'int'])`，而返回值是`int`则`new NativeFunction(address, 'int', argTypes[, options])`，必须要全部匹配，并且第一个参数一定要是函数地址指针。
 

## 1.5 NativeCallback对象
`new NativeCallback(func，rereturn Type，argTypes[，ABI])：`创建一个由`JavaScript`函数`func`实现的新`NativeCallback`，其中`rereturn Type`指定返回类型，`argTypes`数组指定参数类型。您还可以指定`ABI`(如果不是系统默认值)。有关支持的类型和Abis的详细信息，请参见`NativeFunction`。注意，返回的对象也是一个`NativePointer`，因此可以传递给`Interceptor#replace`。当将产生的回调与`Interceptor.replace()`一起使用时，将调用func，并将其绑定到具有一些有用属性的对象，就像`Interceptor.Attach()`中的那样。我们来看一个例子。如下，利用`NativeCallback`做一个函数替换。
```python
Java.perform(function () {
       var add_method = new NativeFunction(Module.findExportByName('libhello.so', 'c_getSum'), 
       'int',['int','int']);
       console.log("result:",add_method(1,2));
       //在这里new一个新的函数，但是参数的个数和返回值必须对应
       Interceptor.replace(add_method, new NativeCallback(function (a, b) {
            return 123;
       }, 'int', ['int', 'int']));
       console.log("result:",add_method(1,2));
    });
```
 

## 结语
本篇咱们学习了非常实用的API，如Interceptor对象对so层导出库函数拦截、NativePointer对象的指针操作、NativeFunction对象的实例化so函数的使用等都是当前灰常好用的函数~建议童鞋了多多尝试~


