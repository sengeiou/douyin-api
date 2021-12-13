# 抖音爬虫获取抖音cookies(CK)


# 分析
1. Cookie中文名称为小型文本文件，指某些网站为了辨别用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）。

1. 有时也用其复数形式Cookies，指某些网站为了辨别用户身份、进行session跟踪而储存在用户本地终端上的数据(通常经过加密)。

1. 抖音核心的cookies是sessionID值, 可嵌套到接口的headers里的Cookie里进行请求。

1. 目前有研究出有三种获取ck的方法:
(1). 通过web端的扫码获取到cookies
(2). 通过app端抖音的账号和密码进行获取到cookies
(3). 通过app端的验证码进行获取cookies

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


## 一、web扫码获取
通过抖音企业管理平台: [https://e.douyin.com/site/](https://e.douyin.com/site/)

#### 抓包分析

 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607044985708-b1587872-7313-4860-af93-ad0a914ad038.png#align=left&display=inline&height=213&name=image.png&originHeight=426&originWidth=2308&size=238173&status=done&style=none&width=1154)
红色标记为 base64 图片, 进行本地保存并打开, 即是登录的二维码
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607045001057-6edfc953-8604-47c5-a367-9d3c214ac5c1.png#align=left&display=inline&height=397&name=image.png&originHeight=794&originWidth=2258&size=374299&status=done&style=none&width=1129)
然后在去实时的请求二维码状态, 数据包的data.status

 

| 字段 | 状态 |
| --- | --- |
| new | 未扫码 |
| scanned | 已扫码待确认登录 |
| confirmed | 已确认登录 |


#### 部分代码截图

 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607045022264-403f62fb-2ade-446a-841b-e39044d20106.png#align=left&display=inline&height=555&name=image.png&originHeight=1110&originWidth=1514&size=183692&status=done&style=none&width=757)

 

## 二、账号密码登录获取Cookie(为保护隐私,打码请理解)

#### 抓包分析

 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607045037376-24ee0d45-08e4-41a5-b670-0c2739fdd29e.png#align=left&display=inline&height=748&name=image.png&originHeight=1496&originWidth=1906&size=351523&status=done&style=none&width=953)

 

###### 请求为POST, 在请求体中mobile和password进行加密, 破解了mobile和password就可以请求登录后cookies里的session值了

### 直接上代码

 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607045067564-0b01ec76-352e-49fe-9d51-6f161aca5c27.png#align=left&display=inline&height=420&name=image.png&originHeight=840&originWidth=2808&size=459684&status=done&style=none&width=1404)
返回的cookies可直接嵌套到请求其他接口的headers头里面, 即可进行进行点赞和关注等操作接口了

 

## 三、发送验证码登录获取Cookie

#### 抓包分析

 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607045092609-59fda56a-f010-424d-998a-f0320a3bdce7.png#align=left&display=inline&height=602&name=image.png&originHeight=1204&originWidth=2848&size=541157&status=done&style=none&width=1424)
红色标识的是将YZ发送到10690032183903进行验证
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607045112035-427675e9-3cba-4375-b427-bb9be2d27723.png#align=left&display=inline&height=621&name=image.png&originHeight=1242&originWidth=2828&size=580731&status=done&style=none&width=1414)
点击登录后, 发送验证接口, 确认发送成功进行登录, 红色标记为登录时的verify_ticket值, 进行验证
验证码状态

 

| 字段 | 状态 |
| --- | --- |
| auth_failed | 未发送验证码 |


### 直接上代码

 
![image.png](https://cdn.nlark.com/yuque/0/2020/png/97322/1607045128503-3b6e39b9-a3bd-48bd-b108-ca0b78cf8b3b.png#align=left&display=inline&height=444&name=image.png&originHeight=888&originWidth=1896&size=293005&status=done&style=none&width=948)
将session_key嵌套到headers里的Cookie里即可


 


___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
