# 抖音协议算法生成，调用so文件生成xgorgon

目前很多 App 的加密签名算法都在so文件中，强行逆向so的话可能会消耗大量时间和资源。

之前用 **xposed** 采用 hook 的方法从程序计算签名，但是需要模拟器或者真机运行这个应用，使用效率不高。

也用过 **jtype** 启动JVM，然后通过 native 对so文件进行调用，因为每次都需要启动JVM，所以效率也不高。

**unidbg** 他不需要运行 app，也无需逆向 so 文件，通过在 app 中找到对应的 JNI 接口，然后用 unicorn 引擎直接调用 so 文件，所以效率相对要高不少。

---


## unidbg特点

- 模拟JNI调用API，可以调用JNI_OnLoad
- 支持JavaVM，JNIEnv
- 模拟系统调用
- 支持ARM32和ARM64
- 内联钩子(Inline Hook)
- Android import hook
- iOS 的一些hook工具 fishhook and substrate and whale hook
- 支持简单的控制台调试器，gdb存根，实验性IDA android调试器服务器，指令跟踪，内存读/写跟踪
- 支持iOS objc和Swift

---

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 



## unidbg环境配置

 
unidbg下载链接： [https://github.com/zhkl0228/unidbg](https://github.com/zhkl0228/unidbg)
IntelliJ IDEA可用于编辑unidbg源

![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607648039184-4e4e9e08-f3b3-4a0b-8529-1e09d39d764c.png#align=left&display=inline&height=174&name=image.png&originHeight=347&originWidth=902&size=44493&status=done&style=none&width=451)

下载完成之后示导入到 IDEA 中，当然你需要准备好java环境(jdk、maven)

![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607648052125-f31bd848-aa4a-4e44-9c8b-b50aad059fb6.png#align=left&display=inline&height=296&name=image.png&originHeight=591&originWidth=577&size=43549&status=done&style=none&width=288.5)
选择Maven
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607648066700-ac62973d-f61b-4d7e-9b70-defa76818dc8.png#align=left&display=inline&height=171&name=image.png&originHeight=341&originWidth=643&size=24172&status=done&style=none&width=321.5)
等待加载完成之后，运行src/…/encrypt 中的TTEncrypt测试用例
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607648079487-0398c2d7-9831-431b-a14f-4f40be337228.png#align=left&display=inline&height=393&name=image.png&originHeight=785&originWidth=1404&size=221478&status=done&style=none&width=702)
如果控制台打印相关调用信息，说明已经导入成功。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607648092442-83e45cf2-fc4a-45c7-9a1e-f3cc7063a6a0.png#align=left&display=inline&height=333&name=image.png&originHeight=665&originWidth=1252&size=140671&status=done&style=none&width=626)

 

---

## 加载libcms.so


 

下面准备调用libcms.so文件中的 leviathan 函数
首先在 src/test/resources 目录下新建文件夹dylib，放入libcms.so文件
**libcms.so** 下载地址: [https://download.csdn.net/download/weixin_43582101/12713664](https://download.csdn.net/download/weixin_43582101/12713664)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607648147457-dd180e36-42c8-40c6-a756-89875f6c601a.png#align=left&display=inline&height=255&name=image.png&originHeight=510&originWidth=427&size=45165&status=done&style=none&width=213.5)
然后我在 /unidbg/unidbg-android/src/test/java/com/sun/jna/ 目录下新建了 JniDispatch128.java文件
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607648161464-60659864-fe02-4415-9c33-dfe00321ed36.png#align=left&display=inline&height=227&name=image.png&originHeight=453&originWidth=366&size=60960&status=done&style=none&width=183)
**JniDispatch128.java**文件内容如下：

 

|  | 备注 |
| --- | --- |
| “com/ss/sys/ces/a” | 需要调用函数所在的Java类完整路径，比如 a/b/c/d 等等，注意需要用/代替. |
| “leviathan(II[B)[B” | 需要调用的函数名，名字是smali语法，可通过jadx等工具查看 |
| “vm.loadLibrary(new File)” | so文件的路径，需要自行修改，最好为绝对路径 |

```python
package com.sun.jna;

import com.github.unidbg.*;
import com.github.unidbg.linux.android.AndroidARMEmulator;
import com.github.unidbg.linux.android.AndroidResolver;
import com.github.unidbg.linux.android.dvm.*;
import com.github.unidbg.memory.Memory;
import com.github.unidbg.memory.MemoryBlock;
import com.github.unidbg.linux.android.dvm.array.ByteArray;



import java.io.File;
import java.io.IOException;

public class JniDispatch128 extends AbstractJni {

    private static LibraryResolver createLibraryResolver() {
        return new AndroidResolver(23);
    }

    private static AndroidEmulator createARMEmulator() {
        return new AndroidARMEmulator("com.sun.jna");
    }

    private final AndroidEmulator emulator;
    private final Module module;
    private final VM vm;

    private final DvmClass Native;

    private JniDispatch128() {
        emulator = createARMEmulator();
        final Memory memory = emulator.getMemory();
        memory.setLibraryResolver(createLibraryResolver());

        vm = emulator.createDalvikVM(null);
        vm.setJni(this);
        vm.setVerbose(true);

		// 自行修改文件路径
        DalvikModule dm = vm.loadLibrary(new File("/Users/Desktop/unidbg/unidbg-android/src/test/resources/dylib/libcms.so"), false);
        dm.callJNI_OnLoad(emulator);
        module = dm.getModule();

        Native = vm.resolveClass("com/ss/sys/ces/a");
   }

    private void destroy() throws IOException {
        emulator.close();
        System.out.println("destroy");
    }

    public static void main(String[] args) throws Exception {

        JniDispatch128 test = new JniDispatch128();

        test.test();

        test.destroy();
    }

    public static String xuzi1(byte[] bArr) {
        if (bArr == null) {
            return null;
        }
        char[] charArray = "0123456789abcdef".toCharArray();
        char[] cArr = new char[(bArr.length * 2)];
        for (int i = 0; i < bArr.length; i++) {
            int b2 = bArr[i] & 255;
            int i2 = i * 2;
            cArr[i2] = charArray[b2 >>> 4];
            cArr[i2 + 1] = charArray[b2 & 15];
        }
        return new String(cArr);
    }


    private void test() {
        String methodSign = "leviathan(II[B)[B";


        byte[] data = "暂时随便写的,这里是url经过处理后的data".getBytes();
        int time = (int) (System.currentTimeMillis() / 1000);

        Native.callStaticJniMethod(emulator, methodSign, -1,time,new ByteArray(vm,data));

        Object ret = Native.callStaticJniMethodObject(emulator, methodSign, -1,time,new ByteArray(vm,data));

        System.out.println("callObject执行结果:"+((DvmObject) ret).getValue());

        byte[] tt = (byte[]) ((DvmObject) ret).getValue();
        System.out.println(new String(tt));
        String s = xuzi1(tt);
        System.out.println(s);
    }
}


```

 
运行main方法即可查看生成出来的xgorgon了
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607648196196-72cbdb4f-b95d-4feb-8c41-a1d5d3db2a20.png#align=left&display=inline&height=84&name=image.png&originHeight=168&originWidth=692&size=53123&status=done&style=none&width=346)


 


