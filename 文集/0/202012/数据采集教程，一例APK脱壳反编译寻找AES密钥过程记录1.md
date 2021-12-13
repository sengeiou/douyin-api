# 抖音数据采集教程，一例APK脱壳反编译寻找AES密钥过程记录


 
应客户需求对一款名为“**主治医师总题库”包名为com.zitibaohe.zhuzhiyishierke）的APP进行采集可行性分析。
  这款APP和服务器的通信使用的是HTTP协议，很容易抓到数据包，可惜返回的数据是加密的，如下图所示。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608943717740-978f58b5-d644-45d8-81b4-450f75e955d3.png#align=left&display=inline&height=251&name=image.png&originHeight=502&originWidth=615&size=66206&status=done&style=none&width=307.5)

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

  根据以往经验，内容应该是被AES加密了。要想还原出明文，必须要反编译拿到KEY才行。
  下载APK文件，用JADX对其进行反编译，发现被加壳了，使用的是360的加壳工具，如下图所示。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608943727201-94c7dc6d-dce3-4943-a2cf-2469e65d21c2.png#align=left&display=inline&height=113&name=image.png&originHeight=227&originWidth=409&size=17182&status=done&style=none&width=204.5)
  必须先脱壳才能反编译到真实的APK源码。脱壳我们使用Xposed + FDex2插件（PS：如果你对Xposed还不熟悉，建议先阅读Xposed相关文章）。
  安装好FDex2插件(不要忘了重启系统)，启动插件，点选要脱壳的APP，如下图所示。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608943737353-dec52de2-f03a-4b05-b0a4-c0bd321bbc51.png#align=left&display=inline&height=239&name=image.png&originHeight=478&originWidth=446&size=64672&status=done&style=none&width=223)
  然后启动目标APP（儿科主治医师总题库）。使用Root Explorer浏览到APP的数据目录（/data/data/com.zitibaohe.zhuzhiyishierke/）下，如果看到多个dex文件（原本该目录下没有这些文件，如下图所示），说明脱壳成功了。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608943748070-bcf4a77e-63ac-4576-bf54-d7ef25362c45.png#align=left&display=inline&height=347&name=image.png&originHeight=694&originWidth=499&size=83393&status=done&style=none&width=249.5)
  将这几个dex文件pull下来，然后依次用JADX对其进行反编译。由于有多个dex，怎么快速定位我们关注的代码在哪一个里呢？我们可以搜一些特征字符串，比如前面抓包看到的请求URL中的个“questions”，如果找到了，说明八成就是这个dex文件（如下图所示）。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608943756902-9f737c8f-827d-4560-87f0-0b00eb5e278f.png#align=left&display=inline&height=327&name=image.png&originHeight=654&originWidth=1139&size=101222&status=done&style=none&width=569.5)
  然后我们再搜索AES相关的关键词比如“AES/”,"SecretKeySpec"或“IvParameterSpec”，最终成功定位（如下图所示），其使用了"AES/CBC/NoPadding"加密算法，对应的KEY和IV都是明文的。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608943766748-b26984b5-0c4d-4bc3-a308-dc5cbde5a0b8.png#align=left&display=inline&height=232&name=image.png&originHeight=463&originWidth=712&size=40901&status=done&style=none&width=356)
  我们来验证下KEY和IV是否正确。借助“AES Online”(http://aes.online-domain-tools.com/)这个在线工具，选择对应的加密算法，输入密文、KEY、IV（如下图所示）。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608943777368-3fa58bdd-8d17-40a8-9b46-f66973b55451.png#align=left&display=inline&height=185&name=image.png&originHeight=369&originWidth=792&size=33148&status=done&style=none&width=396)
  然后点击"Decrypt"，成功还原出明文（如下图所示）。点击“[Download as a binary file]”下载解密后的数据文件，发现内容是JSON格式的，解析之后如下图所示。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608943787665-c73e0ede-0299-41e9-95bc-bf6468b58007.png#align=left&display=inline&height=331&name=image.png&originHeight=661&originWidth=848&size=51856&status=done&style=none&width=424)
  至此，目标实现。
  除了上述通过反编译源码来获取秘钥外（过程很繁琐），我们还可以利用Xposed的方式（你需要先了解Xposed相关知识），通过HOOK javax.crypto.spec.SecretKeySpec和javax.crypto.spec.IvParameterSpec类来截获KEY和IV，操作过程简单，下面介绍两个相关插件。
1.使用Inspeckage（[https://github.com/ac-pm/Inspeckage](https://github.com/ac-pm/Inspeckage)）这个Android动态分析工具。如下图所示是Inspeckage截获到的AES的KEY，与我们上面通过源码获取到的一致（不过没有获取的IV，原因未知）。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608943795611-2ab6570f-ece5-4d85-8578-9dec355a5b68.png#align=left&display=inline&height=238&name=image.png&originHeight=475&originWidth=696&size=81781&status=done&style=none&width=348)
2.我还测了一个叫做CryptoFucker的Xposed插件（[https://github.com/Chenyuxin/CryptoFucker](https://github.com/Chenyuxin/CryptoFucker)），用起来虽然没有Inspeckage方便(没有UI)，不过，效果很好，成功获取到了KEY和IV，如下图所示（HEX形式显示）。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1608943803539-bb9c6654-5397-426c-8d98-19fcf8090100.png#align=left&display=inline&height=234&name=image.png&originHeight=468&originWidth=851&size=69579&status=done&style=none&width=425.5)


 

