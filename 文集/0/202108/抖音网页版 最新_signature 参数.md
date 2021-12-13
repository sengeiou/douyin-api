
抖音最近出了网页版，几乎福利：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628383076012-6ca4a5d8-819c-43a6-a79d-e02324faf48b.png#clientId=u7f010c5f-e757-4&from=paste&height=812&id=u45388973&name=image.png&originHeight=1624&originWidth=2498&originalType=binary&ratio=1&size=4558916&status=done&style=none&taskId=udb0160e8-43d7-49fe-934d-3f78cfaf4c2&width=1249)



___________________
数据采集接口SDK更多信息请[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)
___________________


​

研究了下：发现几个api只有_signature参数加密

## 抖音用户视频列表接口分析

## 1、从抖音 APP 分享个人信息，复制链接，获得个人主页地址，示例：
​


## 2、使用 Chrome 抓包，获取视频列表接口的请求信息
​


#### 接口请求详情
​

参数分析：
​

user_id: 用户ID，可从 HTML 中提取
sec_uid: 空
count: 视频数量
max_cursor: 视频索引位置，用于翻页
aid: 固定值 1128
_signature: 实时签名值，由签名算法计算
dytk: 用户 token，可从 HTML 中提取
​


## 3、定位 _signature 签名算法
​

定位 __M
​


## 4、分析签名算法的执行逻辑
​

① 定义 __M 对象，及其 define 和 require 函数
​

② 执行 __M.define("douyin_falcon:node_modules/byted-acrawler/dist/runtime......" 这段代码
​

③ 执行 _bytedAcrawler = require("douyin_falcon:node_modules/byted-acrawler/dist/runtime")
​

④ 计算签名值 _signature = _bytedAcrawler.sign(user_id)
​

使用 NodeJS 提供签名计算服务
​

此处可能存在跨语言频繁调用的场景，所以使用 grpc 提供服务。
​


## 5、获取的接口示例：

![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628465978802-60bab094-860a-4dac-a7b0-7e56978afc48.png#clientId=u9a4c20a5-d168-4&from=paste&height=271&id=u953bbd7f&name=image.png&originHeight=542&originWidth=1330&originalType=binary&ratio=1&size=184956&status=done&style=none&taskId=ud615380b-ea32-41b3-b53e-51292aacd11&width=665)




![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466016175-2d74faf2-4932-4957-a20a-66952cbf16b2.png#clientId=u9a4c20a5-d168-4&from=paste&height=303&id=u4aa723e8&name=image.png&originHeight=606&originWidth=1466&originalType=binary&ratio=1&size=213414&status=done&style=none&taskId=u3fa9e355-91c2-421f-82a4-0d4ffa5ad79&width=733)




![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466031233-3b5473a1-929d-4aec-a96b-f71de09853c0.png#clientId=u9a4c20a5-d168-4&from=paste&height=408&id=u046514a0&name=image.png&originHeight=815&originWidth=779&originalType=binary&ratio=1&size=82848&status=done&style=none&taskId=u2de73736-d124-47bb-b192-da05ccb3893&width=389.5)




![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628466040518-22cd5d53-7dbd-45fc-98b4-206c22f75f26.png#clientId=u9a4c20a5-d168-4&from=paste&height=315&id=u94281e8b&name=image.png&originHeight=630&originWidth=1530&originalType=binary&ratio=1&size=199712&status=done&style=none&taskId=u2f22a3a4-cee8-4f22-8e90-e60cb6179bb&width=765)

