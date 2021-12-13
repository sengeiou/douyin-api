# 抖音 Web 版签名算法 _signature 解密，抖音协议抖音接口教程

### 抖音用户视频列表接口分析
**1、从抖音 APP 分享个人信息，复制链接，获得个人主页地址，示例：**

- [https://www.iesdouyin.com/share/user/96956380265](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.iesdouyin.com%2Fshare%2Fuser%2F96956380265)
- ​


**2、使用 Chrome 抓包，获取视频列表接口的请求信息**

---



数据采集接口请求参数和返回数据更多信息请[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)

---

**​**



 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297413952-7828f14c-c89c-483c-8a21-84630a584575.png#clientId=u0ad31618-4a4e-4&from=paste&height=377&id=u99c95100&name=image.png&originHeight=753&originWidth=1200&originalType=binary&ratio=1&size=609666&status=done&style=none&taskId=ua72d4164-3767-4b7c-8d17-b797b5de054&width=600)

 

 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297427636-fc5d8589-1d64-4c2a-9e25-ef97dcb57bb4.png#clientId=u0ad31618-4a4e-4&from=paste&height=314&id=uae39544f&name=image.png&originHeight=627&originWidth=1200&originalType=binary&ratio=1&size=183470&status=done&style=none&taskId=u357f8870-c71e-4c5b-ba69-176b6717dc9&width=600)

 



参数分析：

- user_id: 用户ID，可从 HTML 中提取
- sec_uid: 空
- count: 视频数量
- max_cursor: 视频索引位置，用于翻页
- aid: 固定值 1128
- _signature: 实时签名值，由签名算法计算
- dytk: 用户 token，可从 HTML 中提取


**3、定位 _signature 签名算法**

 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297439430-5fba546e-1718-449a-b30c-17c4108fd128.png#clientId=u0ad31618-4a4e-4&from=paste&height=432&id=uf33f26c0&name=image.png&originHeight=864&originWidth=1200&originalType=binary&ratio=1&size=438523&status=done&style=none&taskId=u0a8c5619-2c45-4e42-9bad-d1c97ee9249&width=600)

 

 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297447009-24b3eef9-86be-4094-bc04-d90ef603618e.png#clientId=u0ad31618-4a4e-4&from=paste&height=435&id=ue7839035&name=image.png&originHeight=869&originWidth=1200&originalType=binary&ratio=1&size=451625&status=done&style=none&taskId=u625a3871-9588-41be-b098-4a833b30f7d&width=600)

 

 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297454770-068572a9-c11f-4d03-b208-30bff69c8685.png#clientId=u0ad31618-4a4e-4&from=paste&height=441&id=ue14b522b&name=image.png&originHeight=882&originWidth=1200&originalType=binary&ratio=1&size=498223&status=done&style=none&taskId=ubedf48d5-5c81-49fa-aadb-6a1bff187cc&width=600)

 

 
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297461971-67ac0d12-de36-4696-bb30-8b0c984fcf3a.png#clientId=u0ad31618-4a4e-4&from=paste&height=441&id=u99cd70ef&name=image.png&originHeight=882&originWidth=1200&originalType=binary&ratio=1&size=405831&status=done&style=none&taskId=u72af0f0b-2d64-49d0-adcd-eabf5e8d426&width=600)

 



**4、分析签名算法的执行逻辑**

- ① 定义 __M 对象，及其 define 和 require 函数
- ② 执行 __M.define("douyin_falcon:node_modules/byted-acrawler/dist/runtime......" 这段代码
- ③ 执行 _bytedAcrawler = require("douyin_falcon:node_modules/byted-acrawler/dist/runtime")
- ④ 计算签名值 _signature = _bytedAcrawler.sign(user_id)

### 使用 NodeJS 提供签名计算服务
分析完签名算法的调用过程，就可以搭建 NodeJS 工程来生成签名了，快去试试吧！

---



数据采集接口请求参数和返回数据更多信息请[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)

---


### 中途遇到的问题
**1、userAgent 属性不存在**
Debug 分析抖音 API 请求流程，userAgent 属性在 navigator 对象中，所以需要定义全局对象：


```python
global.navigator = {
    userAgent: "Mozilla/5.0 (iPhone; CPU iPhone OS 11_0 like Mac OS X) AppleWebKit/604.1.38 (KHTML, like Gecko) Version/11.0 Mobile/15A372 Safari/604.1"
};
```


**2、document 对象不存在**

- 解决方式一：删除 document 相关代码
- 解决方式二：[https://stackoverflow.com/questions/32126003/node-js-document-is-not-defined](https://links.jianshu.com/go?to=https%3A%2F%2Fstackoverflow.com%2Fquestions%2F32126003%2Fnode-js-document-is-not-defined)



___________________ 

免责申明：此内容仅供学习交流使用，若侵犯贵方权益，请联系作者删除 
