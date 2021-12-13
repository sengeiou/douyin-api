# 抖音直播间商品列表接口，抖音接口数据采集SDK



# 接口：直播商品列表

### 请求Api
```http
http://主机地址/douyin/liveroom/promotions?token=xxx&room_id=6843198199583378191
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
| room_id | int | 直播间id |


>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 


### 返回示例
```json
{
    "code":200,
    "data":{
        "current_promotion_id":"3436129527946972390",
        "entries":[
            {
                "icon":"http://p3.pstatp.com/origin/321f50008dae87efe41ce",
                "title":"订单",
                "type":2,
                "url":"aweme://webview/?url=https%3A%2F%2Faweme.snssdk.com%2Ffalcon%2Fe_commerce%2Frn%2Forder_dashboard%2F%3Fstatus_font_dark%3D0%26hide_nav_bar%3D1%26loading_bgcolor%3D161823%26should_full_screen%3D1%26enter_from%3Dlive_list_card%26origin_type%3D9902106000&status_font_dark=0&hide_nav_bar=1&loading_bgcolor=161823&should_full_screen=1&enter_from=live_list_card&origin_type=9902106000&rn_schema=aweme%3A%2F%2Freactnative%2F%3Fmodule_name%3Dpage_order_dashboard%26status_font_dark%3D0%26hide_nav_bar%3D1%26loading_bgcolor%3D161823%26should_full_screen%3D1%26channel%3Dfe_lyon_order_dashboard%26bundle%3Dindex.js%26enter_from%3Dlive_list_card%26origin_type%3D9902106000"
            }
        ],
        "expire_time":300000,
        "flash_total":0,
        "has_more":false,
        "min_refresh_gap":10000,
        "next_page_id":"",
        "promotions":[
            Object{...},
            {
                "app_type":0,
                "app_url":"",
                "apply_coupon":0,
                "button_label":"去购买",
                "campaign":false,
                "can_explain":true,
                "can_seckill":false,
                "cos_fee":0,
                "cos_ratio":0,
                "coupons":[
                ],
                "cover":"https://sf6-ttcdn-tos.pstatp.com/img/temai/4d79a9bc80b64c9ffd9412fc45f59c9bwww850-850~tplv-resize:200:0.webp",
                "detail_url":"aweme://goods/seeding/?promotion_id=3436129527946972390&product_id=3436129527946972390&target_uid=3773170526723102&item_id=0&source_page=live&sec_target_uid=MS4wLjABAAAAtvPLMdtyUwsz7C5mYnFRnpVzkJTC4k_AGxjfPhzu8dWQymu9_lzXPHMXp1q5gnGj&meta_param={"enter_from":"live","live_room_id":"6872323400263797518"}&carrier_type=&entrance_info=",
                "elastic_title":"",
                "event_param":{
                },
                "extra":{
                    "coupon_min_price":"0",
                    "start_time":"0"
                },
                "flash_icon":"",
                "flash_type":0,
                "in_stock":true,
                "index":2,
                "item_type":6,
                "label_icon":"https://sf1-ttcdn-tos.pstatp.com/obj/cmp-ecom-alliance/explain_new.png",
                "min_price":5990,
                "next_page":"split",
                "order_url":"",
                "platform":4,
                "platform_icon":"https://sf6-ttcdn-tos.pstatp.com/obj/cmp-ecom-alliance/platform_xiaodian.png",
                "platform_label":"小店",
                "price":99900,
                "product_id":"3436129527946972390",
                "promotion_id":"3436129527946972390",
                "promotion_source":[
                ],
                "sale_num":0,
                "shop_id":3494225,
                "short_half_url":"",
                "short_title":"",
                "skip_style":0,
                "status":2,
                "stock_num":0,
                "title":"k00006三色显瘦牛仔裤"
            },
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
            Object{...}
        ],
        "room_type":0,
        "show_begin_time":0,
        "show_duration":0,
        "status_code":0,
        "status_msg":"",
        "top_limit":100,
        "total":31
    },
    "msg":"success"
}
```
