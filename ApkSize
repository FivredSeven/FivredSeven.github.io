1、图片压缩和转webp
* https://tinypng.com/ 手动压缩
* https://github.com/Omooo/Lavender   
png|jpg图片转webp，亲测demo可用，同样支持aar内的资源转化，不过放到公司项目需要升级gradle和build tool，成本较高。
* https://github.com/didi/booster    package size
* https://github.com/smallSohoSolo/McImage  
同样需要升级gradle，也支持aar内资源，png转webp的时候存在png删不干净导致出现png和webp两个格式的相同文件名文件

2、多Dpi优化
目前公司项目基本按照xxhdpi格式来使用图片，暂无此问题

3、重复资源合并
这个主要是针对同文件不同名字，多个业务使用，减包的程度有限

4、shrinkResource 严格模式

5、资源混淆(兼容 aab 模式) 

6、ARSC 瘦身


https://mp.weixin.qq.com/s/4sA0gJGS3H1zD0eBYZB0Qw
https://mp.weixin.qq.com/s/mUZqvj1mdJdbZQlap4hwRQ
https://mp.weixin.qq.com/s/IVwhWv7p-Unt4aNxdVX-Ug


*********
官方的减包方案
https://developer.android.com/studio/build/shrink-code?hl=zh-cn
1、缩减代码 minifyEnabled true  应用包含许多库依赖项，但只使用它们的一小部分功能时，可以去除不用的部分
2、移除原生库 Android App Bundle
3、缩减资源 shrinkResources true
4、对代码进行混淆处理 mapping.txt
5、代码优化 android.enableR8.fullMode=true
排查 R8 问题
