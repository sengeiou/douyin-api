# 抖音商品搜索列表，抖音API接口数据采集教程


我这里使用模拟器抓的包，params里面有设备信息：device_type、device_brand、device_platform、device_id
DEVICEID可唯一标识一个存储设备，搜索接口基于该参数做有一定的请求限制。
（我贴出来的参数中 设备id是假的，需要更换为自己的）


Post表单参数：
```
data = {
        'cursor': 0,	
        'keyword':'鞠婧祎',
        'count': 20,
        'hot_search': 0,
        'is_pull_refresh': 1,
        'search_source': 'challenge',
        'search_id':None,
        'query_correct_type': 1
        }
```
headers参数：
xgorgon是请求所需的加密参数、xtttoken在搜索接口用于用户身份的标示。

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


```
ts = str(time.time()).split(".")[0]
_rticket = str(time.time() * 1000).split(".")[0]
headers={
        "X-Gorgon":xgorgon,
        "X-Khronos": ts,
        "sdk-version":"1",
        "Accept-Encoding": "gzip",
        "X-SS-REQ-TICKET": _rticket,
        "Host": "aweme.snssdk.com",
        "Connection": "Keep-Alive",
        'User-Agent': 'okhttp/3.10.0.1',
        "x-tt-token":xtttoken
      } 
```
经过测试，搜索接口的 xgorgon 一个大概可以用2分钟。1秒请求1次，请求130条数据需要重新获取xgorgon。
当然也可以每次请求都重新生成xgorgon。
每个账号每天请求搜索接口有次数限制，大概在300左右。
超过限制需要更换账号，另外params也要改变。

## 抖音商品搜索列表Api：

### 请求Api
```http
http://主机地址/douyin/search/goods?token=xxx&keyword=热巴&cursor=20

```

### 

### 请求方式
```http
GET
```

### 

### 参数
| 字段 | 类型 | 说明 |
| --- | --- | --- |
| token | string | 接口授权码 |
| keyword | string | 搜索关键词 |
| cursor | int | 翻页游标，根据结果返回的cursor传入作为下一页翻页参数，初始为0 |

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


### 返回示例
```json
{
    "code":200,
    "data":{
        "cursor":20,
        "extra":{
            "fatal_item_ids":[
            ],
            "logid":"20200914214538010144061078301C11FC",
            "now":1600091139000
        },
        "goods":[
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            Object{...},
            {
                "goods_info":{
                    "commodity_type":9,
                    "gid":"3373777775797344526",
                    "h5_url":"https://m-goods.kaola.com/product/5755274.html",
                    "img":"https://sf1-ttcdn-tos.pstatp.com/obj/temai-bw/1b4fb86c71f4c244babac62a8c019af3ec14b53fwww800-800",
                    "name":"Adidas阿迪达斯女羽绒服迪丽热巴同款冬季CS PUFFER运动保暖连帽羽绒服EI4421",
                    "platform_name":"考拉海购",
                    "schema":"aweme://goods/seeding/?promotion_id=3373777775797344526&target_uid=99514375927&sec_target_uid=MS4wLjABAAAA2I9NdgAKZrz9e0tLm1csyDMNqLESPDm34TdYYqXe8-I&item_id=0&product_id=5755274&meta_param=%7B%22page_type%22%3A1%7D",
                    "schema_type":2,
                    "show_price":58900
                },
                "position":[
                    {
                        "begin":16,
                        "end":17
                    }
                ]
            }
        ],
        "has_more":true,
        "log_pb":{
            "impr_id":"20200914214538010144061078301C11FC"
        },
        "status_code":0
    },
    "msg":"success"
}
```


