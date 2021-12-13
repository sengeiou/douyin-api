# 快手协议ulog快手did注册激活，sig3爬虫抓取签名加密算法

## 快手多个版本sig3参数逆向分析
```
目前已更新:
7.2
7.6
7.7版本
我们需要分析的是sig3参数，所以直接在ida中搜索是否有相关的引用，具体细节略过，有不懂的可以加我交流
我们先看一下大概流程。

```

 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607476855382-1e22cd03-1e29-44a4-b963-59a1f15d0dcb.png#align=left&display=inline&height=514&name=image.png&originHeight=1028&originWidth=1788&size=464447&status=done&style=none&width=894)

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607476868618-7a119589-df8a-4e0f-a4ee-674805af0807.png#align=left&display=inline&height=662&name=image.png&originHeight=1324&originWidth=1934&size=308719&status=done&style=none&width=967)

sig3的复杂程度已经超过dy了，包括动态库加载的方式。
jniOnload倒不需要花太大时间研究，直接hook register方法就能拿到doCommandNative的函数地址。还有就是里面大概率有一些花指令要写脚本修复一下，
关键的是doCommandNative内部的根据command转发的函数地址是动态注册的，当时研究的时候他们的签名没有用VMP，主要还是花指令和代码膨胀要花些时间处理。


 



