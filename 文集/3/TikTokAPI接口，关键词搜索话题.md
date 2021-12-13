# TikTokAPI接口，关键词搜索话题

**请求API：**
```http
http://主机地址/tk/search/challenges?token=xxx&keyword=beauty
```
**请求方式：**
```http
GET
```
**​**

**参数：**

| 参数名 | 必选 | 类型 | 说明 |
| --- | --- | --- | --- |
| token | 是 | string | 接口授权码 |
| keyword | 是 | string | 搜索关键词 |
| cursor | 否 | int | 翻页游标，根据结果返回的cursor传入作为下一页翻页参数，初始为0 |


>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU1VrcWR5ekZFYmJO) 

**返回示例**
```json
{
	"code": 200,
	"msg": "成功",
	"data": {
		"challenge_list": [
			{
				"challenge_info": {
					"cid": "86929725",
					"cha_name": "beautyls",
					"desc": "",
					"schema": "aweme:\/\/aweme\/challenge\/detail?cid=86929725",
					"user_count": 12990,
					"share_info": {
						"share_url": "https:\/\/m.tiktok.com\/h5\/share\/tag\/86929725.html?name=beautyls&u_code=0&language=en&_d=dk4e43gkjcg74h&share_challenge_id=86929725&source=h5_m",
						"share_weibo_desc": "Check out #beautyls on TikTok!",
						"share_desc": "Check out #beautyls on TikTok!",
						"share_title": "It is a becoming a big trend on TikTok now! Click here: beautyls",
						"bool_persist": 0,
						"share_title_myself": "",
						"share_title_other": "",
						"share_signature_url": "",
						"share_signature_desc": "",
						"share_quote": "",
						"share_desc_info": "Check out #beautyls on TikTok!"
					},
					"connect_music": [],
					"type": 1,
					"sub_type": 0,
					"is_pgcshow": false,
					"collect_stat": 0,
					"is_challenge": 0,
					"view_count": 135310217,
					"is_commerce": false,
					"hashtag_profile": "",
					"cha_attrs": null,
					"banner_list": null,
					"extra_attr": {
						"is_live": false
					},
					"show_items": null,
					"search_cha_name": "beautyls",
					"search_highlight": null
				},
				"position": null,
				"items": null
			},
			{
				"challenge_info": {
					"cid": "34087013",
					"cha_name": "beautybay",
					"desc": "",
					"schema": "aweme:\/\/aweme\/challenge\/detail?cid=34087013",
					"user_count": 13669,
					"share_info": {
						"share_url": "https:\/\/m.tiktok.com\/h5\/share\/tag\/34087013.html?name=beautybay&u_code=0&language=en&_d=dk4e43gkjcg74h&share_challenge_id=34087013&source=h5_m",
						"share_weibo_desc": "Check out #beautybay on TikTok!",
						"share_desc": "Check out #beautybay on TikTok!",
						"share_title": "It is a becoming a big trend on TikTok now! Click here: beautybay",
						"bool_persist": 0,
						"share_title_myself": "",
						"share_title_other": "",
						"share_signature_url": "",
						"share_signature_desc": "",
						"share_quote": "",
						"share_desc_info": "Check out #beautybay on TikTok!"
					},
					"connect_music": [],
					"type": 1,
					"sub_type": 0,
					"is_pgcshow": false,
					"collect_stat": 0,
					"is_challenge": 0,
					"view_count": 77218613,
					"is_commerce": false,
					"hashtag_profile": "",
					"cha_attrs": null,
					"banner_list": null,
					"extra_attr": {
						"is_live": false
					},
					"show_items": null,
					"search_cha_name": "beautybay",
					"search_highlight": null
				},
				"position": null,
				"items": null
			},
			{
				"challenge_info": {
					"cid": "192019",
					"cha_name": "beautyful",
					"desc": "",
					"schema": "aweme:\/\/aweme\/challenge\/detail?cid=192019",
					"user_count": 74605,
					"share_info": {
						"share_url": "https:\/\/m.tiktok.com\/h5\/share\/tag\/192019.html?name=beautyful&u_code=0&language=en&_d=dk4e43gkjcg74h&share_challenge_id=192019&source=h5_m",
						"share_weibo_desc": "Check out #beautyful on TikTok!",
						"share_desc": "Check out #beautyful on TikTok!",
						"share_title": "It is a becoming a big trend on TikTok now! Click here: beautyful",
						"bool_persist": 0,
						"share_title_myself": "",
						"share_title_other": "",
						"share_signature_url": "",
						"share_signature_desc": "",
						"share_quote": "",
						"share_desc_info": "Check out #beautyful on TikTok!"
					},
					"connect_music": [],
					"type": 2,
					"sub_type": 0,
					"is_pgcshow": false,
					"collect_stat": 0,
					"is_challenge": 0,
					"view_count": 285809795,
					"is_commerce": false,
					"hashtag_profile": "",
					"cha_attrs": null,
					"banner_list": null,
					"extra_attr": {
						"is_live": false
					},
					"show_items": null,
					"search_cha_name": "beautyful",
					"search_highlight": null
				},
				"position": null,
				"items": null
			},
			{
				"challenge_info": {
					"cid": "15602676",
					"cha_name": "beautybox",
					"desc": "",
					"schema": "aweme:\/\/aweme\/challenge\/detail?cid=15602676",
					"user_count": 9621,
					"share_info": {
						"share_url": "https:\/\/m.tiktok.com\/h5\/share\/tag\/15602676.html?name=beautybox&u_code=0&language=en&_d=dk4e43gkjcg74h&share_challenge_id=15602676&source=h5_m",
						"share_weibo_desc": "Check out #beautybox on TikTok!",
						"share_desc": "Check out #beautybox on TikTok!",
						"share_title": "It is a becoming a big trend on TikTok now! Click here: beautybox",
						"bool_persist": 0,
						"share_title_myself": "",
						"share_title_other": "",
						"share_signature_url": "",
						"share_signature_desc": "",
						"share_quote": "",
						"share_desc_info": "Check out #beautybox on TikTok!"
					},
					"connect_music": [],
					"type": 1,
					"sub_type": 0,
					"is_pgcshow": false,
					"collect_stat": 0,
					"is_challenge": 0,
					"view_count": 37522428,
					"is_commerce": false,
					"hashtag_profile": "",
					"cha_attrs": null,
					"banner_list": null,
					"extra_attr": {
						"is_live": false
					},
					"show_items": null,
					"search_cha_name": "beautybox",
					"search_highlight": null
				},
				"position": null,
				"items": null
			},
			{
				"challenge_info": {
					"cid": "3201738",
					"cha_name": "beautymakeup",
					"desc": "",
					"schema": "aweme:\/\/aweme\/challenge\/detail?cid=3201738",
					"user_count": 10193,
					"share_info": {
						"share_url": "https:\/\/m.tiktok.com\/h5\/share\/tag\/3201738.html?name=beautymakeup&u_code=0&language=en&_d=dk4e43gkjcg74h&share_challenge_id=3201738&source=h5_m",
						"share_weibo_desc": "Check out #beautymakeup on TikTok!",
						"share_desc": "Check out #beautymakeup on TikTok!",
						"share_title": "It is a becoming a big trend on TikTok now! Click here: beautymakeup",
						"bool_persist": 0,
						"share_title_myself": "",
						"share_title_other": "",
						"share_signature_url": "",
						"share_signature_desc": "",
						"share_quote": "",
						"share_desc_info": "Check out #beautymakeup on TikTok!"
					},
					"connect_music": [],
					"type": 1,
					"sub_type": 0,
					"is_pgcshow": false,
					"collect_stat": 0,
					"is_challenge": 0,
					"view_count": 46535233,
					"is_commerce": false,
					"hashtag_profile": "",
					"cha_attrs": null,
					"banner_list": null,
					"extra_attr": {
						"is_live": false
					},
					"show_items": null,
					"search_cha_name": "beautymakeup",
					"search_highlight": null
				},
				"position": null,
				"items": null
			},
			{
				"challenge_info": {
					"cid": "296662",
					"cha_name": "beautyvideo",
					"desc": "",
					"schema": "aweme:\/\/aweme\/challenge\/detail?cid=296662",
					"user_count": 2349,
					"share_info": {
						"share_url": "https:\/\/m.tiktok.com\/h5\/share\/tag\/296662.html?name=beautyvideo&u_code=0&language=en&_d=dk4e43gkjcg74h&share_challenge_id=296662&source=h5_m",
						"share_weibo_desc": "Check out #beautyvideo on TikTok!",
						"share_desc": "Check out #beautyvideo on TikTok!",
						"share_title": "It is a becoming a big trend on TikTok now! Click here: beautyvideo",
						"bool_persist": 0,
						"share_title_myself": "",
						"share_title_other": "",
						"share_signature_url": "",
						"share_signature_desc": "",
						"share_quote": "",
						"share_desc_info": "Check out #beautyvideo on TikTok!"
					},
					"connect_music": [],
					"type": 1,
					"sub_type": 0,
					"is_pgcshow": false,
					"collect_stat": 0,
					"is_challenge": 0,
					"view_count": 39272019,
					"is_commerce": false,
					"hashtag_profile": "",
					"cha_attrs": null,
					"banner_list": null,
					"extra_attr": {
						"is_live": false
					},
					"show_items": null,
					"search_cha_name": "beautyvideo",
					"search_highlight": null
				},
				"position": null,
				"items": null
			},
			{
				"challenge_info": {
					"cid": "4769879",
					"cha_name": "beautyboy",
					"desc": "",
					"schema": "aweme:\/\/aweme\/challenge\/detail?cid=4769879",
					"user_count": 5100,
					"share_info": {
						"share_url": "https:\/\/m.tiktok.com\/h5\/share\/tag\/4769879.html?name=beautyboy&u_code=0&language=en&_d=dk4e43gkjcg74h&share_challenge_id=4769879&source=h5_m",
						"share_weibo_desc": "Check out #beautyboy on TikTok!",
						"share_desc": "Check out #beautyboy on TikTok!",
						"share_title": "It is a becoming a big trend on TikTok now! Click here: beautyboy",
						"bool_persist": 0,
						"share_title_myself": "",
						"share_title_other": "",
						"share_signature_url": "",
						"share_signature_desc": "",
						"share_quote": "",
						"share_desc_info": "Check out #beautyboy on TikTok!"
					},
					"connect_music": [],
					"type": 1,
					"sub_type": 0,
					"is_pgcshow": false,
					"collect_stat": 0,
					"is_challenge": 0,
					"view_count": 44841011,
					"is_commerce": false,
					"hashtag_profile": "",
					"cha_attrs": null,
					"banner_list": null,
					"extra_attr": {
						"is_live": false
					},
					"show_items": null,
					"search_cha_name": "beautyboy",
					"search_highlight": null
				},
				"position": null,
				"items": null
			}
		],
		"cursor": 40,
		"has_more": 1,
		"is_match": false,
		"status_code": 0,
		"qc": "",
		"keyword_disabled": 0,
		"ad_info": [],
		"extra": {
			"now": 1628760173000,
			"logid": "202108120922530102450190321C225B89",
			"fatal_item_ids": [],
			"search_request_id": ""
		},
		"log_pb": {
			"impr_id": "202108120922530102450190321C225B89"
		},
		"global_doodle_config": {
			"keyword": "beauty",
			"search_channel": "musically_challenge",
			"new_source": ""
		}
	}
}
```
