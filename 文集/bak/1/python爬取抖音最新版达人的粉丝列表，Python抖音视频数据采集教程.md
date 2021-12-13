
## python爬取抖音最新版达人的粉丝列表，Python抖音视频数据采集教程

### 项目场景：

获取抖音某一个账户的粉丝信息，俗话说同行的客户就是你的客户，获取到他的粉丝数据，对他的粉丝进行分析、挖掘，此博客只是对我爬取过程中遇到问题进行记录，仅供学习！
​


>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


​


### 问题描述：

#### 1、模拟器连接fiddle之后浏览器有网，抖音没网。
​

使用模拟器一定要创建安卓5.x版本！！！使用7.x的会有一堆坑。
下面是逍遥模拟器创建安卓5.x版本方法（夜神同理）
​


![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628727818974-2a7591c9-e917-4111-84a7-673645aaa064.png#clientId=u989e043e-e4d4-4&from=paste&height=296&id=ub9dbb8ac&name=image.png&originHeight=592&originWidth=731&originalType=binary&ratio=1&size=27144&status=done&style=none&taskId=u681c97dc-092e-4429-b2c8-578d28e86ab&width=365.5)
​



![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628727836619-53bde07a-60d0-4991-8402-af4484cb7937.png#clientId=u989e043e-e4d4-4&from=paste&height=386&id=ud174af24&name=image.png&originHeight=772&originWidth=763&originalType=binary&ratio=1&size=207670&status=done&style=none&taskId=ue8893485-dcce-490c-b6ef-29b90d1b07a&width=381.5)



之后点击启动就可。
[
](https://blog.csdn.net/weixin_43485669/article/details/110091956)
​


#### 2、使用低版本抖音抓取获取不到粉丝信息，不过可以获取到视频评论等其他信息。
​

经过测试抖音14.5.0一下版本，无法刷新出粉丝信息，后面决定还是使用最新版本抖音进行爬取。
​

附上抖音各版本apk: [https://www.wandoujia.com/apps/7461948/history](https://www.wandoujia.com/apps/7461948/history)
​

​


#### 3、fiddle不能自动保存抓到的json文件。
​

点击fiddle中 FiddlerScript 按钮
​


![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628727873561-6cf1911b-9e65-43a2-a330-aa519040e2dc.png#clientId=u989e043e-e4d4-4&from=paste&height=60&id=u8999883d&name=image.png&originHeight=120&originWidth=784&originalType=binary&ratio=1&size=15229&status=done&style=none&taskId=udb3cb785-039f-4e80-ba0e-7e1c44c96b2&width=392)



​

在OnBeforeResponse函数中添加代码如下
​

oSession.uriContains(“[https://api3-normal-c-hl.amemv.com/](https://api3-normal-c-hl.amemv.com/)”)是你需要抓包文件的域名
var filename = “D:/sof” + “/” + sps + “.json”; 修改成保存json文件路径
​

```http
static function OnBeforeResponse(oSession: Session) {
       
        if (m_Hide304s && oSession.responseCode == 304) {
            oSession["ui-hide"] = "true";
        }
        
        if (oSession.uriContains("https://api3-normal-c-hl.amemv.com/")){
            var strBody=oSession.GetResponseBodyAsString();
            var sps = oSession.PathAndQuery.slice(-58,);
            //FiddlerObject.alert(sps)
            var filename = "D:/sof" + "/" + sps + ".json";
            var curDate = new Date();   
            var sw : System.IO.StreamWriter;  
            if (System.IO.File.Exists(filename)){  
                sw = System.IO.File.AppendText(filename);  
                sw.Write(strBody); 
            }  
            else{  sw = System.IO.File.CreateText(filename); 
                sw.Write(strBody);
            }  
            sw.Close(); 
            sw.Dispose(); 
        }
     }

```


修改问脚本记得保存。
​

结果展示：
遇到挺多问题，不过结果还算不错，开心！
​


![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628727912388-f05ebc28-f72c-4e0d-a9d7-d2d0585f65d8.png#clientId=u989e043e-e4d4-4&from=paste&height=258&id=u88379c39&name=image.png&originHeight=515&originWidth=840&originalType=binary&ratio=1&size=106883&status=done&style=none&taskId=ue5412895-e68c-4dc1-857e-a5408587592&width=420)




![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628727921823-705e3df6-0084-46b9-9906-ca6b38327ec0.png#clientId=u989e043e-e4d4-4&from=paste&height=14&id=u4313e61c&name=image.png&originHeight=28&originWidth=961&originalType=binary&ratio=1&size=3864&status=done&style=none&taskId=uf00fb5ae-88ff-4fe8-839d-ef3cd50534d&width=480.5)




![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628727931987-e0d3ceaa-5154-467a-b036-541d9e439890.png#clientId=u989e043e-e4d4-4&from=paste&height=405&id=u6aa78520&name=image.png&originHeight=809&originWidth=1037&originalType=binary&ratio=1&size=150098&status=done&style=none&taskId=u3692dd61-92b6-44ec-9d92-1c517e97664&width=518.5)
​


[
](https://blog.csdn.net/weixin_43485669/article/details/114490206)


