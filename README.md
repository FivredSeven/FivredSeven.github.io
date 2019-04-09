# FivredSeven.github.io
57's blog
    
        
## 简单分析Android应用各类别前五名的app内部存储文件        
### 视频TOP5 （抖音、爱奇艺、腾讯视频、优酷、哔哩哔哩）
#### 抖音
~/Android/data/com.ss.android.ugc.aweme    
/cache/cache/xxxxxx  缓存视频路径，后缀名加上.mp4即可直接播放,只要用户看过都会放在这里，如果是用户主动保存的视频，会放到用户相册里去。。缓存视频会定期清理，每次启动后，除去当天和前一天的不会清理，其他日期的视频缓存都会清空。    

/cache/picture/fresco_cache/v2/ols100.1/0……99/xxxxxx.cnt 缓存图片路径，用的是fresco，后缀改为png也可直接查看，图片缓存不会清理。


#### 爱奇艺
~/Android/data/com.qiyi.video/    
/files/app/download/video/ 用户主动下载的视频路径，每个视频对应一个数字命名的文件夹（可能是视频id之类），每个文件夹内会存视频封面图以及.qsv文件。    

/files/Pictures/EditPersonalTemp/xxxxxx.jpg 用户修改的头像路径 IMG_20190329_115009.jpg = 年月日_时分秒


#### 腾讯视频
~/Android/data/com.tencent.qqlive/    
/files/.webapp/ 这里有很多js、图、css等    
/files/cache/ 这里有很多有价值的缓存    以下文件名中的uid为真实用户uid替换
* uid_userinfo:解析可得到头像，位置，qq号等
* uiduserinfo:解析可得到vip等级，vip起止时间，积分等
* CircleFriendListModel_uid_1.cache:好友昵称和头像列表
* DokiMainNavModel_uid.cache:doki明星名称，加入的圈子
* HomeTimeLineModel_uid.cache:时间轴，观看视频名称
以上文件内容需UTF-8转码    
/files/videos_x4exh/ 下载的视频放在这里，安全性比较高


#### 优酷
~/Android/data/com.youku.phone    
/cache/images/ 和抖音类似的图片缓存    
~/youku/offlinedata 缓存的视频放这里，每个视频一个文件夹，文件夹内有图片及视频文件，更重要的是有一个info文件，Json格式，内容包含视频名称，类型，id，时长等等


#### 哔哩哔哩
~/Android/data/tv/danmaku.bili/    
/download/视频id 每个视频一个文件夹    
/download/1/entry.json 内容包含视频信息，名称(title)，封面，画质，时长，弹幕数量，上传者等。    
/cache/ImagePipeLine/  和抖音类似的图片缓存

<hr style="height:3px;border:none;border-top:3px double red;" />



