# 数据采集教程，以抖音为例，反编译抖音apk


#### 前言
这一篇来讲解一下如何反编译抖音的APK，了解过后反编译其实也是挺简单的

#### 工具

1. apktoolApktool是google提供的apk的编译工具。官方使用说明：[https://ibotpeaches.github.io/Apktool/install/](https://ibotpeaches.github.io/Apktool/install/)在安装过程中需注意：快速检查Apktool 2.x (apktool 1.5.2以后的版本)
   - 是否安装 Java 1.7？
   - 在命令行执行 java –version 查看是否返回1.7?
   - 如果不是，请安装 Java7并进行相关的环境配置


2. Apktool 1.x (apktook 1.5.2之前的版本)

   - 是否安装 Java 1.6 或更高的版本？
   - 在命令行执行 java –version 查看是否返回1.6 或更高的版本?
   - 如果不是，请安装Java 6 或 Java 7。


3. —>check电脑是否安装apktool（区分apktool 2.x和apktool 1.x）运行需要的java环境。

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

Apktool 2.x的安装

   - Windows:
   - 下载脚本链接并存为apktool.bat 下载 apktool-2 (下载最新)
   - 重命名下载的jar文件为apktool.jar 移动文件(apktool.jar & apktool.bat)到电脑的windows目录，一般为C://Windows
   - 如果你没有权限访问C://Windows，你可以将这两个文件放在任意目录下，然后将该目录添加到环境变量path中 尝试在命令行中运行
   - Linux: （略）
   - Mac OS X: （略）
注意 - 脚本文件bat并不是必须的，但该文件非常有用，你可以避免反复输入 java-jar apktool.jar。
1. Apktool 1.x安装

   - Windows:
   - 下载apktool-install-windows-* 文件
   - 下载apktool-* 文件
   - 解压以上文件并移动到windows目录下
   - Linux: （略）
   - Mac OS X: （略）

参考：[https://www.cnblogs.com/mliangchen/p/5079783.html](https://www.cnblogs.com/mliangchen/p/5079783.html)

1. dex2jar
这个目录用来将Android打包后的.dex文件转化为.jar包。
下载地址：[http://sourceforge.net/projects/dex2jar/files/](http://sourceforge.net/projects/dex2jar/files/)

1. jd-gui
查看APK中classes.dex转化成出的jar文件，即源码文件
[https://github.com/java-decompiler/jd-gui/releases](https://github.com/java-decompiler/jd-gui/releases)（可以在GitHub下载）


参考：[https://blog.csdn.net/s13383754499/article/details/78914592](https://blog.csdn.net/s13383754499/article/details/78914592)

---


 
也可以在我提供的网盘中下载这些工具包~
链接：[https://pan.baidu.com/s/1XyEmSCBSuUaAAXBsQdREng](https://pan.baidu.com/s/1XyEmSCBSuUaAAXBsQdREng) 密码：21kz
文件中包含了抖音的apk，想要自己获取apk的话，可以用自己的手机上的浏览器搜索抖音然后下载，得到的就是apk文件
文件如下
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944282145-2f49178a-cc8d-47c7-8436-56f449ac7f07.png#align=left&display=inline&height=67&name=image.png&originHeight=134&originWidth=614&size=18335&status=done&style=none&width=307)
一、先将apktool.bat和apktool.jar移动到C:\Windows下，需要管理员权限点击即可；接着测试，打开cmd命令窗口，输入apktool，若出现
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944295579-8e420c1c-7dd2-4a53-a073-058aed90f6ef.png#align=left&display=inline&height=242&name=image.png&originHeight=484&originWidth=1049&size=62374&status=done&style=none&width=524.5)
则说明成功，可以使用apktool；若不可以，则请看上述安装apktool2的注意事项
二、接着，cd到这些工具的目录后，输入命令：
java -jar apktool.jar d -f D:\Python\PycharmProject\F-APK\douyin.apk -o douyin
就可以看到生成了douyin的文件夹
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944309792-5bdaa3dc-0e05-4b48-bded-f346722593be.png#align=left&display=inline&height=173&name=image.png&originHeight=346&originWidth=828&size=30775&status=done&style=none&width=414)
里面是这样的
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944321468-95542d63-11a9-46c3-ae0f-8585c1703124.png#align=left&display=inline&height=133&name=image.png&originHeight=266&originWidth=588&size=26645&status=done&style=none&width=294)
这样就是用apktool抖音apk反编译得到图片、XML配置、语言资源等文件的过程
三、将要反编译的抖音APK后缀名改为.rar或者 .zip，并解压得到其中的classes.dex文件
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944336346-5811d0d3-4ab7-4e8d-b869-45bdb3df13ae.png#align=left&display=inline&height=246&name=image.png&originHeight=491&originWidth=613&size=60895&status=done&style=none&width=306.5)
将这个classes.dex放到之前解压出来的工具dex2jar-2.0文件夹内
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944349575-3d61cbb5-eb96-49f8-896c-6f5087eb1bf3.png#align=left&display=inline&height=244&name=image.png&originHeight=488&originWidth=623&size=69696&status=done&style=none&width=311.5)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944361363-67d9c51e-7dc3-4f07-9a12-61bb7718826f.png#align=left&display=inline&height=238&name=image.png&originHeight=476&originWidth=604&size=59501&status=done&style=none&width=302)
在命令行下定位到dex2jar.bat所在目录，输入”d2j-dex2jar classes.dex”
得到如下：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944374709-417fe0ea-c992-4a75-8ced-7f2f14c79f39.png#align=left&display=inline&height=54&name=image.png&originHeight=108&originWidth=750&size=8768&status=done&style=none&width=375)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944386485-359980d6-6768-4ae6-ac77-5c3d57682162.png#align=left&display=inline&height=33&name=image.png&originHeight=65&originWidth=604&size=8448&status=done&style=none&width=302)
至此，就可以看到生成的jar文件了
四、最后就是用jd-gui工具将class文件反编译成java源代码
双击打开jd-gui.exe
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944400127-3345b490-5065-4f4f-9546-cd9bc29527d5.png#align=left&display=inline&height=54&name=image.png&originHeight=108&originWidth=597&size=10047&status=done&style=none&width=298.5)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944410340-1e0fdc64-b464-49e4-943d-fd16b537f27e.png#align=left&display=inline&height=200&name=image.png&originHeight=399&originWidth=596&size=22088&status=done&style=none&width=298)
然后将classes-dex2jar.jar文件拖入即可
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608944424920-0b2ebd11-5d05-4352-9717-8199f8060eb7.png#align=left&display=inline&height=261&name=image.png&originHeight=522&originWidth=806&size=55483&status=done&style=none&width=403)
这样一来反编译就成功啦！是不是挺简单！
怎么破解抖音的三个加密参数就……接着摸索吧！


 

