# 抖音API：同城视频列表

**说明：**
> 此接口为同城推荐，只需传城市编码和经纬度，无翻页，相同参数每次请求结果不同（此接口一次返回20条）


**请求API：**

- `http://主机地址/douyin/video/nearby?token=xxx&city=330400`

**请求方式：**

- GET

>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

**参数：**

| 参数名 | 必选 | 类型 | 说明 |
| :--- | :--- | :--- | --- |
| token | 是 | string | 接口授权码 |
| city | 是 | int | 城市编码，可从城市编码列表获取 |
| longitude | 否 | double | 经度，示例：_113.373219_ |
| latitude | 否 | double | 维度，示例：_23.126153_ |

**
**返回示例**
**
```json
{
    "code": 200,
    "msg": "成功",
    "data": {
        "status_code": 0,
        "has_more": 1,
        "aweme_list": [
            {
                "aweme_id": "6911567780107586311",
                "create_time": 1609224775,
                "author": {
                    "uid": "4503659967175903",
                    "short_id": "3117705967",
                    "nickname": "离离圆雪梨",
                    "avatar_larger": {
                        "uri": "1080x1080\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0",
                        "url_list": [
                            "https:\/\/p3-dy-ipv6.byteimg.com\/img\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0~c5_1080x1080.jpeg?from=4010531038",
                            "https:\/\/p26-dy.byteimg.com\/img\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0~c5_1080x1080.jpeg?from=4010531038",
                            "https:\/\/p1-dy-ipv6.byteimg.com\/img\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0~c5_1080x1080.jpeg?from=4010531038"
                        ]
                    },
                    "avatar_thumb": {
                        "uri": "100x100\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0",
                        "url_list": [
                            "https:\/\/p6-dy-ipv6.byteimg.com\/img\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0~c5_100x100.jpeg?from=4010531038",
                            "https:\/\/p29-dy.byteimg.com\/img\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0~c5_100x100.jpeg?from=4010531038",
                            "https:\/\/p9-dy.byteimg.com\/img\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0~c5_100x100.jpeg?from=4010531038"
                        ]
                    },
                    "avatar_medium": {
                        "uri": "720x720\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0",
                        "url_list": [
                            "https:\/\/p1-dy-ipv6.byteimg.com\/img\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0~c5_720x720.jpeg?from=4010531038",
                            "https:\/\/p9-dy.byteimg.com\/img\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0~c5_720x720.jpeg?from=4010531038",
                            "https:\/\/p3-dy-ipv6.byteimg.com\/img\/tos-cn-avt-0015\/d3edc64f2e2f37c22a48d07618d1a0c0~c5_720x720.jpeg?from=4010531038"
                        ]
                    },
                    "follow_status": 0,
                    "room_id": 6911567780107586311,
                    "followers_detail": null,
                    "platform_sync_info": null,
                    "geofencing": null,
                    "room_cover": {
                        "uri": "webcast\/6901257341287598861",
                        "url_list": [
                            "http:\/\/p6-webcast-dycdn.byteimg.com\/img\/webcast\/6901257341287598861~tplv-obj.image",
                            "http:\/\/p9-webcast-dycdn.byteimg.com\/img\/webcast\/6901257341287598861~tplv-obj.image",
                            "http:\/\/p3-webcast-dycdn.byteimg.com\/img\/webcast\/6901257341287598861~tplv-obj.image"
                        ]
                    },
                    "cover_url": null,
                    "item_list": null,
                    "new_story_cover": null,
                    "type_label": null,
                    "ad_cover_url": null,
                    "relative_users": null,
                    "cha_list": null,
                    "sec_uid": "MS4wLjABAAAANAH-BWn_XY6rMuSAtLSn7MNr2ANxG5RGUwjgBecCFrTkx4C7L3hc5oqtRL6Ol0WK",
                    "need_points": null,
                    "homepage_bottom_toast": null,
                    "can_set_geofencing": null,
                    "white_cover_url": null,
                    "user_tags": null,
                    "ban_user_functions": null
                },
                "cha_list": null,
                "text_extra": null,
                "share_info": [],
                "video_labels": null,
                "aweme_type": 101,
                "image_infos": null,
                "position": null,
                "uniqid_position": null,
                "comment_list": null,
                "author_user_id": 4503659967175903,
                "geofencing": null,
                "video_text": null,
                "label_top_text": null,
                "promotions": null,
                "nickname_position": null,
                "challenge_position": null,
                "long_video": null,
                "interaction_stickers": null,
                "origin_comment_ids": null,
                "commerce_config_data": null,
                "room": {
                    "room_id": 6911567780107586311,
                    "user_count": 10,
                    "citycode": "",
                    "latitude": 0,
                    "longitude": 0,
                    "height": 0,
                    "width": 0,
                    "with_linkmic": false
                },
                "stream_url": {
                    "rtmp_pull_url": "http:\/\/pull-flv-l26.douyincdn.com\/stage\/stream-107993237045117067_or4.flv",
                    "id": "107993237045117067"
                },
                "total_user_count": 10,
                "title": "热舞直播间",
                "video_feed_tag": "直播中",
                "anchors": null,
                "hybrid_label": null,
                "geofencing_regions": null,
                "cover_labels": null
            },
            {
                "aweme_id": "6911593020253670158",
                "create_time": 1609230652,
                "author": {
                    "uid": "69180437360",
                    "short_id": "172369837",
                    "nickname": "LLOVEY U服装工作室",
                    "avatar_larger": {
                        "uri": "1080x1080\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53",
                        "url_list": [
                            "https:\/\/p29-dy.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~c5_1080x1080.jpeg?from=4010531038",
                            "https:\/\/p3-dy-ipv6.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~c5_1080x1080.jpeg?from=4010531038",
                            "https:\/\/p6-dy-ipv6.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~c5_1080x1080.jpeg?from=4010531038"
                        ]
                    },
                    "avatar_thumb": {
                        "uri": "100x100\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53",
                        "url_list": [
                            "https:\/\/p9-dy.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~c5_100x100.jpeg?from=4010531038",
                            "https:\/\/p6-dy-ipv6.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~c5_100x100.jpeg?from=4010531038",
                            "https:\/\/p3-dy-ipv6.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~c5_100x100.jpeg?from=4010531038"
                        ]
                    },
                    "avatar_medium": {
                        "uri": "720x720\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53",
                        "url_list": [
                            "https:\/\/p26-dy.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~c5_720x720.jpeg?from=4010531038",
                            "https:\/\/p3-dy-ipv6.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~c5_720x720.jpeg?from=4010531038",
                            "https:\/\/p9-dy.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~c5_720x720.jpeg?from=4010531038"
                        ]
                    },
                    "follow_status": 0,
                    "room_id": 6911593020253670158,
                    "followers_detail": null,
                    "platform_sync_info": null,
                    "geofencing": null,
                    "room_cover": {
                        "uri": "tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53",
                        "url_list": [
                            "http:\/\/p1-webcast-dycdn.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~tplv-obj.image",
                            "http:\/\/p6-webcast-dycdn.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~tplv-obj.image",
                            "http:\/\/p3-webcast-dycdn.byteimg.com\/img\/tos-cn-avt-0015\/7838b662be929bfe65ba446b9e785d53~tplv-obj.image"
                        ]
                    },
                    "cover_url": null,
                    "item_list": null,
                    "new_story_cover": null,
                    "type_label": null,
                    "ad_cover_url": null,
                    "relative_users": null,
                    "cha_list": null,
                    "sec_uid": "MS4wLjABAAAA70sqR9AUL746hLKh3XYuPeVuVfs9Uk6Ife-kRLY8kd0",
                    "need_points": null,
                    "homepage_bottom_toast": null,
                    "can_set_geofencing": null,
                    "white_cover_url": null,
                    "user_tags": null,
                    "ban_user_functions": null
                },
                "cha_list": null,
                "text_extra": null,
                "share_info": [],
                "video_labels": null,
                "aweme_type": 101,
                "image_infos": null,
                "position": null,
                "uniqid_position": null,
                "comment_list": null,
                "author_user_id": 69180437360,
                "geofencing": null,
                "video_text": null,
                "label_top_text": null,
                "promotions": null,
                "nickname_position": null,
                "challenge_position": null,
                "long_video": null,
                "interaction_stickers": null,
                "origin_comment_ids": null,
                "commerce_config_data": null,
                "room": {
                    "room_id": 6911593020253670158,
                    "user_count": 20,
                    "citycode": "",
                    "latitude": 0,
                    "longitude": 0,
                    "height": 0,
                    "width": 0,
                    "with_linkmic": false
                },
                "stream_url": {
                    "rtmp_pull_url": "http:\/\/pull-l3.douyincdn.com\/stage\/stream-107993623991156818_or4.flv",
                    "id": "107993623991156818"
                },
                "total_user_count": 20,
                "title": "LLOV...正在直播",
                "video_feed_tag": "直播中",
                "anchors": null,
                "hybrid_label": null,
                "geofencing_regions": null,
                "cover_labels": null
            }
        ],
        "poi_info_list": null,
        "current_city": {
            "code": "330800",
            "name": "衢州",
            "en": "Quzhou",
            "show_name": "衢州",
            "show_type": 0,
            "ad_code": "330802",
            "country_code": "CN",
            "nearby_label_name": "柯城",
            "nearby_tab_name": "柯城",
            "current_position_name": "柯城",
            "districts": null
        },
        "poi_class_info_list": null,
        "poi_op_tab_list": null,
        "poi_rank_card_list": null,
        "poi_op_card_list": null,
        "school_auth": 0,
        "nearby_type": 0,
        "extra": {
            "now": 1609231383000,
            "logid": "202012291643020101980602241E00BF1D",
            "fatal_item_ids": []
        },
        "log_pb": {
            "impr_id": "202012291643020101980602241E00BF1D"
        }
    }
}
```
