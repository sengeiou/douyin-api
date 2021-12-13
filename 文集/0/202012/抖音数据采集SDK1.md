# DouyinSDK  抖音SDK 抖音数据采集 抖音直播数据


> 
> Github仓库：[DouyinSDK](https://github.com/Video-Hub/douyin-sdk)
>**短视频直播数据采集接口SDK请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

## 0. 初始化

```python
from douyin import AwemeSDK

token = 'xxxxxxxx'
host  = 'http://xxxxx.xx.xx'
sdk   = AwemeSDK(token,host)

```

## 1.用户详情
```python

result = sdk.GetUserInfo('100000004548')

```

## 2.用户作品
```python

result = sdk.GetUserPosts('100000004548')

```

## 3.用户喜欢视频
```python

result = sdk.GetUserFavourites('100000004548')

```

## 4.用户商品橱窗
```python

result = sdk.GetUserPromotions('100000004548')

```

## 5.视频评论
```python

result = sdk.GetVideoComments('6785429036362386696')

```

## 28.视频详情
```python

result = sdk.GetVideoDetail(6849103450475711752)

```

## 6.视频带货信息
```python

result = sdk.GetVideoPromotions('6785429036362386696')

```

## 7.视频评论的子评论
```python

result = sdk.GetVideoCommentReplies('6785429036362386696','6775738616267554829')

```

## 8.话题详情
```python

result = sdk.GetChallengeDetail('1671619888543757')

```

## 9.获取话题（challenge）下的视频
```python

result = sdk.GetChallengeVideos('1671619888543757')

```

## 10.地点详情
```python

result = sdk.GetPoiDetail('6666714767266187271')

```

## 11.获取地点（poi）下的视频
```python

result = sdk.GetPoiVideos('6666714767266187271')

```

## 12.带货视频推荐
```python

result = sdk.GetPromotionVideosFeed()

```

## 13.带货商品promotion详情
```python

result = sdk.GetPromotionInfo('60772262244900')

```

## 14.同款商品带货视频推荐
```python

result = sdk.GetPromotionSameVideos('60772262244900')

```

## 15.获取直播间弹幕/进入直播间观众/刷礼物/关注主播 信息
```python

result = sdk.GetLiveRoomChats('6843198199583378191')

```
## 16.获取直播间带货商品信息
```python

result = sdk.GetLiveRoomPromotions('6843198199583378191')

```
## 17.获取直播间信息
```python

result = sdk.GetLiveRoomInfo('6843198199583378191')

```

## 18.查询直播间是否开播
```python

result = sdk.GetLiveRoomStatus('6843198199583378191')

```
## 19.获取直播用户信息
```python

result = sdk.GetUserLiveInfo('6843198199583378191')

```

## 20.实时明星爱DOU榜
```python

result = sdk.RealStarBoard()

```

## 21.实时热点榜
```python

result = sdk.RealHotBoard()

```

## 22.实时好物榜
```python

result = sdk.RealGoodsBoard()

```

## 23.最热视频榜单
```python

result = sdk.RealHotVideos()

```
## 24.热门话题推荐
```python

result = sdk.RealHotChallenges()

```

## 25.实时品牌榜
```python

result = sdk.RealBrandBoard(7)

```

## 26.品牌类别接口
```python

result = sdk.BrandCategories()

```

## 27.热榜品牌详情
```python

result = sdk.BrandDetail(2,9117)

```

## 29.关键词搜索用户
```python

result = sdk.SearchUsers('美女')

```

## 30.关键词搜索视频
```python

result = sdk.SearchVideos('美女')

```

