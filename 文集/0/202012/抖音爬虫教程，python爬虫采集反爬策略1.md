# 抖音爬虫教程，python爬虫采集反爬策略


### 一、爬虫与反爬简介
爬虫就是我们利用某种程序代替人工批量读取、获取网站上的资料信息。而反爬则是跟爬虫的对立面，是竭尽全力阻止非人为的采集网站信息，二者相生相克，水火不容，到目前为止大部分的网站都还是可以轻易的爬取资料信息。

爬虫想要绕过被反的策略就是尽可能的让服务器人你不是机器程序，所以在程序中就要把自己伪装成浏览器访问网站，这可以极大程度降低被反的概率，那如何做到伪装浏览器呢？

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


##### 1.可以使用请求头（headers）来掩饰自己，其中最常用的就是User Agent（中文名为用户代理），是Http协议中的一部分，属于头域的组成部分，User Agent也简称 UA 。

它是一个特殊字符串头，是一种向访问网站提供你所使用的浏览器类型及版本、操作系统及版本、浏览器内核、等信息的标识；表示当前访问服务器的身份信息，如果同一个身份过于频繁的访问服务器会被识别为机器身份，遭到反爬的打击，所以需要频繁的更改User-Agent信息；一般User-Agent字段包括以下几个信息：浏览器标识 (操作系统标识; 加密等级标识; 浏览器语言) 渲染引擎标识 版本信息；

##### 2.使用不同的User-Agent来规避反爬策略

比如：

- Accept：客户端支持的数据类型，用逗号隔开，是有顺序的，分号前面是主类型，分号后是子类型；
- Accept-Encoding：指定浏览器可以支持的web服务器返回内容压缩编码类型；
- Accept-Language：浏览器可接受的自然语言的类型；
- Connection：设置HTTP连接的持久化，通常都是Keep-Alive；
- Host：服务器的域名或IP地址，如果不是通用端口，还包含该端口号；
- Referer：指当前请求的URL是在什么地址引用的；
```python
user_agent_list = [
    "Opera/9.80 (X11; Linux i686; U; hu) Presto/2.9.168 Version/11.50",
    "Opera/9.80 (X11; Linux i686; U; ru) Presto/2.8.131 Version/11.11",
    "Opera/9.80 (X11; Linux i686; U; es-ES) Presto/2.8.131 Version/11.11",
    "Mozilla/5.0 (Windows NT 5.1; U; en; rv:1.8.1) Gecko/20061208 Firefox/5.0 Opera 11.11",
    "Opera/9.80 (X11; Linux x86_64; U; bg) Presto/2.8.131 Version/11.10",
    "Opera/9.80 (Windows NT 6.0; U; en) Presto/2.8.99 Version/11.10",
    "Opera/9.80 (Windows NT 5.1; U; zh-tw) Presto/2.8.131 Version/11.10",
    "Opera/9.80 (Windows NT 6.1; Opera Tablet/15165; U; en) Presto/2.8.149 Version/11.1",
    "Opera/9.80 (X11; Linux x86_64; U; Ubuntu/10.10 (maverick); pl) Presto/2.7.62 Version/11.01",
    "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36",
    "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.97 Safari/537.36",
    "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:70.0) Gecko/20100101 Firefox/70.0",
    "Opera/9.80 (X11; Linux i686; Ubuntu/14.10) Presto/2.12.388 Version/12.16",
    "Opera/9.80 (Windows NT 6.0) Presto/2.12.388 Version/12.14",
    "Mozilla/5.0 (Windows NT 6.0; rv:2.0) Gecko/20100101 Firefox/4.0 Opera 12.14",
    "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.0) Opera 12.14",
    "Opera/12.80 (Windows NT 5.1; U; en) Presto/2.10.289 Version/12.02",
    "Opera/9.80 (Windows NT 6.1; U; es-ES) Presto/2.9.181 Version/12.00",
    "Opera/9.80 (Windows NT 5.1; U; zh-sg) Presto/2.9.181 Version/12.00",
    "Opera/12.0(Windows NT 5.2;U;en)Presto/22.9.168 Version/12.00",
    "Opera/12.0(Windows NT 5.1;U;en)Presto/22.9.168 Version/12.00",
    "Mozilla/5.0 (Windows NT 5.1) Gecko/20100101 Firefox/14.0 Opera/12.0",
    "Opera/9.80 (Windows NT 6.1; WOW64; U; pt) Presto/2.10.229 Version/11.62",
    "Opera/9.80 (Windows NT 6.0; U; pl) Presto/2.10.229 Version/11.62",
    "Opera/9.80 (Macintosh; Intel Mac OS X 10.6.8; U; fr) Presto/2.9.168 Version/11.52",
    "Opera/9.80 (Macintosh; Intel Mac OS X 10.6.8; U; de) Presto/2.9.168 Version/11.52",
    "Opera/9.80 (Windows NT 5.1; U; en) Presto/2.9.168 Version/11.51",
    "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; de) Opera 11.51",
    "Opera/9.80 (X11; Linux x86_64; U; fr) Presto/2.9.168 Version/11.50",
]
referer_list = ["https://www.test.com/", "https://www.baidu.com/"]

```
获取随机数，即每次采集都会根据随机数提取随机用户代理、引用地址（注：若有多个页面循环采集，最好采集完单个等待个几秒钟再继续采集，减小服务器的压力。）：
```python
import random
import re, urllib.request, lxml.html
import requests
import time, random

def get_randam(data):
    return random.randint(0, len(data)-1)
def crawl():
    headers = {
        'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8',
        'Accept-Encoding': 'gzip, deflate',
        'Accept-Language': 'zh-CN,zh;q=0.9',
        'Connection': 'keep-alive',
        'host': 'test.com',
        'Referer': 'https://test.com/',
    }
    random_index = get_randam(user_agent_list)
    random_agent = user_agent_list[random_index]
    headers['User-Agent'] = random_agent
    random_index_01 = get_randam(referer_list)
    random_agent_01 = referer_list[random_index_01]
    headers['Referer'] = random_agent_01
    session = requests.session()
    url = "https://www.test.com/"
    html_data = session.get(url, headers=headers, timeout=180)
    html_data.raise_for_status()
    html_data.encoding = 'utf-8-sig'
    data = html_data.text
    data_doc = lxml.html.document_fromstring(data)
    ...(对网页数据进行解析、提取、存储等)
    time.sleep(random.randint(3, 5))

```

##### 3.使用代理IP来规避反爬：同一个ip大量请求了对方服务器，有更大的可能性会被识别为爬虫，ip就有可能被暂时被封。
根据代理ip的匿名程度，代理ip可以分为下面四类：

- 透明代理（Transparent Proxy）Transparent Proxy)：透明代理虽然可以直接“隐藏”你的IP地址，但是还是可以查到你是谁。
- 匿名代理（Anonymous Proxy）：匿名代理比透明代理进步了一点：别人只能知道你用了代理，无法知道你是谁。
- 混淆代理（Distorting Proxies）：与匿名代理相同，如果使用了混淆代理，别人还是能知道你在用代理，但是会得到一个假的IP地址，伪装的更逼真
- 高匿代理（Elite proxy或High Anonymity Proxy）：可以看出来，高匿代理让别人根本无法发现你是在用代理，所以是最好的选择。
**在使用的使用，毫无疑问使用高匿代理效果最好**

下面我采用免费的高匿代理IP进行采集：
```python
#代理IP： https://www.xicidaili.com/nn
import requests
proxies = {
"http": "http://117.30.113.248:9999",
"https": "https://120.83.120.157:9999"
}
r=requests.get("https://www.baidu.com", proxies=proxies)
r.raise_for_status()
r.encoding = 'utf-8-sig'
print(r.text)

```

**注意：踩坑经历，之前误把proxies里面的key设置成大写的HTTP/HTTPS,导致请求不走代理，过了几个月才发现这个问题，头皮发麻啊**

### 二、总结

之前也经常写一些采集亚马逊的爬虫，但是采集没多久就被识别出来是程序爬虫，会默认跳到一个robotecheck页面，也就是叫你输入一个图片验证码，只是为了验证到底是不是人为在访问他们的网站。

- amazon的防爬虫机制在只有ip (没有cookie) 的时候防ip，在有cookie的时候，是防ip+cookie，也即对于一个ip，一个cookie被防了可以换一个cookie。
- 有cookie的时候触发防爬虫robotcheck的可能性小很多，触发的机制个人猜测是有叠加效应的，即一次使用同一个ip+header短时间内访问多次(1秒内访问至少3次以上)是不会触发robotecheck的，大概在积累了8到9次的短时间多访问（理由是上面很多实验都是在第9个请求后开始出现block），才会激发。而这个忍耐度在有cookie的时候会放得更宽。


**所以我们在爬取某网站时，最好要做到IP轮询变换，cookie会话最好也要有，再来就是频率不要太快太频，只要控制好个度，我相信被反爬的概率会大大降低。**


