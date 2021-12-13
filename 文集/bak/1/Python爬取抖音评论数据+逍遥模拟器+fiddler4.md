## Python爬取抖音评论数据+逍遥模拟器+fiddler4

最近写文章需要用到抖音评论数据，抓取路径有两条：​
1、构造访问参数；
2、模拟器抓包。
​

​

第一种方案虽然高效，但是抖音加密算法一旦更改，就没用了。
第二种比较朴实无华，旱涝保收。
​


>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


​


### 第一步：配置环境
​

Python3：[https://www.python.org/downloads/](https://www.python.org/downloads/)
Fiddler：[https://www.telerik.com/download/fiddler](https://www.telerik.com/download/fiddler)
逍遥模拟器：[https://www.xyaz.cn/](https://www.xyaz.cn/)
​


### 第二步：安装安卓APP+证书
​

在模拟器中安装Xposed+JustTrustMe（用于解决证书验证问题），安装抖音。
​

Fiddler设置"enable the Tools > Options > HTTPS > Decrypt HTTPS traffic option."，手机端打开电脑端IP，如：127.2.2.2:8888，下载证书进行安装。
​


![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628555239850-69eb4ab0-192e-4e28-b3ea-cc4fb16caa5c.png#clientId=uf6a8c384-b704-4&from=paste&height=410&id=u76efe958&name=image.png&originHeight=820&originWidth=1531&originalType=binary&ratio=1&size=106902&status=done&style=none&taskId=ucd54d8a2-ad31-47ee-96f4-1cd51a44d1d&width=765.5)
​


​


### 第三步：配置网络


1、将模拟器WIFI名称设置成电脑WIFI名称
​

2、WIFI代理设置为电脑私有IP，如：127.2.2.2，端口号：8888
​

3、Fiddler设置"Tools > Options >Connections"，勾选Allow remote computers to connect选项，然后重启Fiddler
​


![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628555253488-e4dc3388-41ff-4e5a-8658-b5d2b86b8747.png#clientId=uf6a8c384-b704-4&from=paste&height=185&id=u8a4e2a90&name=image.png&originHeight=370&originWidth=583&originalType=binary&ratio=1&size=27721&status=done&style=none&taskId=ub8600a4f-2b61-431a-a9ed-b900771da0b&width=291.5)
​



### 第四步：编辑FiddlerScript
​

```python
if(oSession.uriContains("https://aweme/v2/comment/list")){
            var strBody=oSession.GetResponseBodyAsString();
            var sps = oSession.PathAndQuery.slice(-58,);
            //FiddlerObject.alert(sps)
            var timestamp=new Date().getTime();
            var filename = "D:/douyin" + "/" + sps + timestamp + ".json";
            var curDate = new Date(); 
            var sw : System.IO.StreamWriter; 
            if (System.IO.File.Exists(filename)){ 
                sw = System.IO.File.AppendText(filename); 
                sw.Write(strBody); 
            } 
            else{ 
                sw = System.IO.File.CreateText(filename); 
                sw.Write(strBody); 
            } 
            sw.Close(); 
            sw.Dispose(); 
        }
```
​


###  第五步，查看效果
​


![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628555275933-419a92e6-1028-46e2-8a25-05550798cf0d.png#clientId=uf6a8c384-b704-4&from=paste&height=513&id=ub9554b2c&name=image.png&originHeight=1025&originWidth=1916&originalType=binary&ratio=1&size=977125&status=done&style=none&taskId=u9546a558-3a4d-4aec-b99e-e39aa077860&width=958)
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628555298321-5288bf3c-7e3d-46f8-ac31-e662cbf0ed78.png#clientId=uf6a8c384-b704-4&from=paste&height=290&id=u83574e41&name=image.png&originHeight=580&originWidth=907&originalType=binary&ratio=1&size=50734&status=done&style=none&taskId=uc6ca5b87-9bc8-4865-86e4-fb181592ada&width=453.5)



​

拿到数据之后，是json格式，长相如下图。还要进行解析，清洗和格式化。
​


![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628555312758-c30c132a-b8a6-4445-8ce2-c30aad8dd95a.png#clientId=uf6a8c384-b704-4&from=paste&height=435&id=uda0fd091&name=image.png&originHeight=870&originWidth=1237&originalType=binary&ratio=1&size=103658&status=done&style=none&taskId=uf2798f54-1a2b-44c8-888c-42ca6b0afc5&width=618.5)
​


整理成EXCEL格式之后，长这样：
​


![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628555327292-0031719a-2b1f-4583-b974-eed08031394d.png#clientId=uf6a8c384-b704-4&from=paste&height=388&id=ud7f030db&name=image.png&originHeight=775&originWidth=1864&originalType=binary&ratio=1&size=1785289&status=done&style=none&taskId=u7a5574aa-5257-40c4-8921-c037cd0741f&width=932)
​


[
](https://blog.csdn.net/chaishen10000/article/details/106344634)
