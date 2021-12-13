# 抖音_signature参数分析，抖音协议抖音接口教程

以抖音作者页为例：
[https://www.iesdouyin.com/share/user/xxx](https://www.iesdouyin.com/share/user/xxx)
从接口可以得到作者发布的视频列表：
[https://www.iesdouyin.com/web/api/v2/aweme/post/?user_id=xxx&sec_uid=&count=21&max_cursor=0&aid=1128&_signature=etVQHxATJIUJI1pwOqp2dnrVUA&dytk=dd51884f959fa649020e74cbd09044e0](https://www.iesdouyin.com/web/api/v2/aweme/post/?user_id=xxx&sec_uid=&count=21&max_cursor=0&aid=1128&_signature=etVQHxATJIUJI1pwOqp2dnrVUA&dytk=dd51884f959fa649020e74cbd09044e0)
​


---



数据采集接口请求参数和返回数据更多信息请[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)

---

​


参数里dytk从作者页抽取得到，_signature参数为js生成，打断点找到_signature的生成位置
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297259657-a156d39b-4f1d-47ac-818b-29d024b77a86.png#clientId=u5daacf81-3de9-4&from=paste&height=423&id=ue56aad81&name=image.png&originHeight=846&originWidth=1906&originalType=binary&ratio=1&size=535745&status=done&style=none&taskId=u11062da7-ff38-487a-8a23-68e2fad8ef4&width=953)
​



![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297274721-4e3055e0-e4f0-45ed-897d-1425eb3f9ec1.png#clientId=u5daacf81-3de9-4&from=paste&height=383&id=ude9c35c2&name=image.png&originHeight=766&originWidth=1922&originalType=binary&ratio=1&size=530236&status=done&style=none&taskId=u77e2073c-f9ed-4c19-b3ea-37e1714c9a2&width=961)
nonce就是作者id，找到_bytedAcrawler的生成位置。
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297291922-590a48a5-5acd-4a79-834e-830d4f43e7d6.png#clientId=u5daacf81-3de9-4&from=paste&height=371&id=u47b36392&name=image.png&originHeight=742&originWidth=2198&originalType=binary&ratio=1&size=614630&status=done&style=none&taskId=uc8046e6f-953e-494f-99f3-b010f7863ca&width=1099)
![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297321494-ec29dfa7-e8d9-49c0-8f53-892ff611af6c.png#clientId=u5daacf81-3de9-4&from=paste&height=317&id=ucb96c2d6&name=image.png&originHeight=634&originWidth=2218&originalType=binary&ratio=1&size=508289&status=done&style=none&taskId=u41a346ee-563d-47a4-9f82-b88befcf97d&width=1109)
再找__M的生成位置
​



![image.png](https://cdn.nlark.com/yuque/0/2021/png/97322/1628297334729-226db91b-f7fc-4d9e-a3d1-cd658da30576.png#clientId=u5daacf81-3de9-4&from=paste&height=195&id=u74c3f084&name=image.png&originHeight=390&originWidth=1680&originalType=binary&ratio=1&size=198580&status=done&style=none&taskId=u6fcff8df-b56a-4375-bf68-c50a0b4f6c5&width=840)



验证找到的地方是否正确：
在console中调用对应的生成方法
```python
dycs = __M.require("douyin_falcon:node_modules/byted-acrawler/dist/runtime") ;

signc = dycs.sign(作者id)
```

---



数据采集接口请求参数和返回数据更多信息请[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR)

---

这时候会发现signc与接口中的_signature字段一致。
因此讲__M生成部分的代码define sign那一块的代码都贴到一个html里。并预先写入一个作者id打印出对应的signature。如果直接打开html会发现与实际的_signature有出入，因此可以好好看看有没有哪块js给window对象赋值了。
​

最后html加入该参数就能拿到正确的_signature字段。

[
](https://blog.csdn.net/okm6666/article/details/106438089)

___________________
免责申明：此内容仅供学习交流使用，若侵犯贵公司的权益，联系作者删除