# 抖音用户直播批量查询接口，抖音API接口数据采集教程，抖音SDK




## 抖音用户直播批量查询Api：

### 请求Api
```http
http://主机地址/douyin/user/rooms?token=xxx&uid=96137740927,97577356905,9902809696

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
| uid | string | 用户id，多个用逗号（,）隔开，最多10个 |

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


### 返回示例
```json
{
    "code": 200,
    "msg": "成功",
    "data": {
        "data": {
            "room_id": {
                "96137740927": 6918197739978492686,
                "97577356905": 6918527343418002176,
                "99028096965": 6918533391520697095
            },
            "room_id_str": [],
            "live_room_mode": [],
            "live_room_mode_str": []
        },
        "extra": {
            "now": 1610849688248
        },
        "status_code": 0
    }
}

```


