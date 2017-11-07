# IPC机制

跨进程通信

## Android多进程模式
多进程模式
在Android中,使用多进程可以在AndroidManifest文件里给需要多进程的组件增加
android:process=":remote"
android:process="com.hjunlin.second"
这两种设置多进程的方式是有区别的。
1. 第一种方式声明的属于当前应用的私有进程
2. 第二种方式声明的是全局进程,其他应用是有机会与该进程数据共享。

什么情况能在不同应用中共享数据呢?这里有摘抄一段描述:

>Android系统为每个应用分配一个唯一的UID，具有相同UID的应用才能共享数据。
两个应用通过ShareUID跑在同一个进程中是有要求的，需要这两个应用有相同的ShareUID并且签名相同才可以。
在这种情况下，它们可以互相访问对方的私有数据，比如data目录，组件信息等。
如果运行在同一进程,甚至能互相访问内存数据。

当然多进程模式下有很多优点,比如可以获取多份内存空间,或者有些模块需要运行在单独进程不被其他影响,例如音乐播放等等。
不过多进程模式下的缺点也不容小视。
* 静态成员和单例模式完全失效
* 线程同步机制完全失效
* SharePreference的可靠性下降
* Application会多次创建

## IPC基础概念介绍
### Serializable && Parcelable
Serializable是java提供的一个序列化接口,而Parcelable是android提供的。简单对比分析一下。
Serializable简单易用,直接implement就行,无需实现方法,但缺点是内部使用反射,序列化的过程比较耗时,而且序列化过程中会
创建许多临时对象,容易触发垃圾回收。
Parcelable速度快,效率高,但是所需代码量大,且阅读性低。
那么速度快多少呢? 网上已经有很多人验证过,并得出了结论。Parcelable比Serializable 快了十倍左右。
我们完全可以忍受它代码量大的缺点而使用到它高效率的一面。那有什么办法来减轻大量的模板代码呢?
有的,github上有很多牛人都写好了框架,我推荐一个吧:
> https://github.com/sockeqwe/ParcelablePlease




