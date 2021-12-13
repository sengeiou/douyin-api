# 抖音Api：音乐搜索列表

## 抖音视频Api、抖音直播Api、抖音评论采集、抖音弹幕采集、抖音爬虫、抖音去水印、抖音视频下载、抖音视频解析
## 抖音直播数据、抖音数据采集、抖音直播监控

### 免责声明
## If you have any questions, you can exchange and study, Telegram: @zver7777
## https://t.me/zver7777
## Skype: live:.cid.b7d7ced6e23d0c41
```


## 抖音音乐搜索列表Api：

### 请求Api
```http
http://主机地址/douyin/search/music?token=xxx&keyword=热巴&cursor=20

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

###

### 返回示例
```json

{
    "code":200,
    "data":{
        "ad_info":{
        },
        "cursor":20,
        "extra":{
            "fatal_item_ids":[
            ],
            "logid":"202009142141120100260780841E1BAB11",
            "now":1600090873000,
            "search_request_id":""
        },
        "has_more":0,
        "log_pb":{
            "impr_id":"202009142141120100260780841E1BAB11"
        },
        "music":[
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
                "album":"",
                "artists":[
                ],
                "audition_duration":60,
                "author":"热巴吉存",
                "author_deleted":false,
                "author_position":null,
                "binded_challenge_id":0,
                "collect_stat":0,
                "cover_hd":{
                    "height":720,
                    "uri":"ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f",
                    "url_list":[
                        "https://p26-dy.byteimg.com/aweme/1080x1080/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                        "https://p29-dy.byteimg.com/aweme/1080x1080/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                        "https://p3-dy-ipv6.byteimg.com/aweme/1080x1080/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp"
                    ],
                    "width":720
                },
                "cover_large":{
                    "height":720,
                    "uri":"ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f",
                    "url_list":[
                        "https://p26-dy.byteimg.com/aweme/720x720/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                        "https://p29-dy.byteimg.com/aweme/720x720/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                        "https://p3-dy-ipv6.byteimg.com/aweme/720x720/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp"
                    ],
                    "width":720
                },
                "cover_medium":{
                    "height":720,
                    "uri":"ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f",
                    "url_list":[
                        "https://p26-dy.byteimg.com/aweme/200x200/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                        "https://p29-dy.byteimg.com/aweme/200x200/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                        "https://p3-dy-ipv6.byteimg.com/aweme/200x200/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp"
                    ],
                    "width":720
                },
                "cover_thumb":{
                    "height":720,
                    "uri":"ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f",
                    "url_list":[
                        "https://p26-dy.byteimg.com/aweme/100x100/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                        "https://p29-dy.byteimg.com/aweme/100x100/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                        "https://p3-dy-ipv6.byteimg.com/aweme/100x100/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp"
                    ],
                    "width":720
                },
                "dmv_auto_show":false,
                "duration":60,
                "external_song_info":[
                ],
                "extra":"{"has_edited":0,"beats":{},"douyin_beats_info":{},"hotsoon_review_time":-1,"music_label_id":1112,"aggregate_exempt_conf":[],"reviewed":0,"review_unshelve_reason":0,"schedule_search_time":0}",
                "id":6872216793570609154,
                "id_str":"6872216793570609154",
                "is_audio_url_with_cookie":false,
                "is_commerce_music":false,
                "is_del_video":false,
                "is_matched_metadata":false,
                "is_original":false,
                "is_original_sound":false,
                "is_pgc":true,
                "is_restricted":false,
                "is_video_self_see":false,
                "lyric_short_position":null,
                "mid":"6872216793570609154",
                "music_chart_ranks":null,
                "mute_share":false,
                "offline_desc":"",
                "owner_handle":"",
                "owner_nickname":"",
                "play_url":{
                    "height":720,
                    "uri":"http://p9-dy.byteimg.com/obj/iesmusic-cn-local/v1/tt-obj/a34916fda67fbd5d5c58a0d3c95e894d.mp3",
                    "url_list":[
                        "http://p9-dy.byteimg.com/obj/iesmusic-cn-local/v1/tt-obj/a34916fda67fbd5d5c58a0d3c95e894d.mp3",
                        "http://p1-dy.byteimg.com/obj/iesmusic-cn-local/v1/tt-obj/a34916fda67fbd5d5c58a0d3c95e894d.mp3"
                    ],
                    "width":720
                },
                "position":null,
                "prevent_download":false,
                "prevent_item_download_status":0,
                "preview_end_time":0,
                "preview_start_time":234.09,
                "reason_type":0,
                "shoot_duration":60,
                "source_platform":10036,
                "status":1,
                "tag_list":null,
                "title":"爱的岁月-剪辑版",
                "unshelve_countries":null,
                "user_count":0
            }
        ],
        "music_info_list":[
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
                "card_type":1,
                "music":{
                    "album":"",
                    "artists":[
                    ],
                    "audition_duration":60,
                    "author":"热巴吉存",
                    "author_deleted":false,
                    "author_position":null,
                    "binded_challenge_id":0,
                    "collect_stat":0,
                    "cover_hd":{
                        "height":720,
                        "uri":"ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f",
                        "url_list":[
                            "https://p26-dy.byteimg.com/aweme/1080x1080/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                            "https://p29-dy.byteimg.com/aweme/1080x1080/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                            "https://p3-dy-ipv6.byteimg.com/aweme/1080x1080/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp"
                        ],
                        "width":720
                    },
                    "cover_large":{
                        "height":720,
                        "uri":"ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f",
                        "url_list":[
                            "https://p26-dy.byteimg.com/aweme/720x720/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                            "https://p29-dy.byteimg.com/aweme/720x720/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                            "https://p3-dy-ipv6.byteimg.com/aweme/720x720/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp"
                        ],
                        "width":720
                    },
                    "cover_medium":{
                        "height":720,
                        "uri":"ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f",
                        "url_list":[
                            "https://p26-dy.byteimg.com/aweme/200x200/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                            "https://p29-dy.byteimg.com/aweme/200x200/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                            "https://p3-dy-ipv6.byteimg.com/aweme/200x200/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp"
                        ],
                        "width":720
                    },
                    "cover_thumb":{
                        "height":720,
                        "uri":"ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f",
                        "url_list":[
                            "https://p26-dy.byteimg.com/aweme/100x100/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                            "https://p29-dy.byteimg.com/aweme/100x100/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp",
                            "https://p3-dy-ipv6.byteimg.com/aweme/100x100/ies-music/storm_cover_3a3f4c95c6a09c30789f60ddeb790c0f.webp"
                        ],
                        "width":720
                    },
                    "dmv_auto_show":false,
                    "duration":60,
                    "external_song_info":[
                    ],
                    "extra":"{"has_edited":0,"beats":{},"douyin_beats_info":{},"hotsoon_review_time":-1,"music_label_id":1112,"aggregate_exempt_conf":[],"reviewed":0,"review_unshelve_reason":0,"schedule_search_time":0}",
                    "id":6872216793570609154,
                    "id_str":"6872216793570609154",
                    "is_audio_url_with_cookie":false,
                    "is_commerce_music":false,
                    "is_del_video":false,
                    "is_matched_metadata":false,
                    "is_original":false,
                    "is_original_sound":false,
                    "is_pgc":true,
                    "is_restricted":false,
                    "is_video_self_see":false,
                    "lyric_short_position":null,
                    "mid":"6872216793570609154",
                    "music_chart_ranks":null,
                    "mute_share":false,
                    "offline_desc":"",
                    "owner_handle":"",
                    "owner_nickname":"",
                    "play_url":{
                        "height":720,
                        "uri":"http://p9-dy.byteimg.com/obj/iesmusic-cn-local/v1/tt-obj/a34916fda67fbd5d5c58a0d3c95e894d.mp3",
                        "url_list":[
                            "http://p9-dy.byteimg.com/obj/iesmusic-cn-local/v1/tt-obj/a34916fda67fbd5d5c58a0d3c95e894d.mp3",
                            "http://p1-dy.byteimg.com/obj/iesmusic-cn-local/v1/tt-obj/a34916fda67fbd5d5c58a0d3c95e894d.mp3"
                        ],
                        "width":720
                    },
                    "position":null,
                    "prevent_download":false,
                    "prevent_item_download_status":0,
                    "preview_end_time":0,
                    "preview_start_time":234.09,
                    "reason_type":0,
                    "shoot_duration":60,
                    "source_platform":10036,
                    "status":1,
                    "tag_list":null,
                    "title":"爱的岁月-剪辑版",
                    "unshelve_countries":null,
                    "user_count":0
                },
                "musics":null
            }
        ],
        "qc":"",
        "status_code":0
    },
    "msg":"success"
}
```
