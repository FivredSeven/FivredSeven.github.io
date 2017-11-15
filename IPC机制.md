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

## IPC
### Serializable && Parcelable
Serializable是java提供的一个序列化接口,而Parcelable是android提供的。简单对比分析一下。
Serializable简单易用,直接implement就行,无需实现方法,但缺点是内部使用反射,序列化的过程比较耗时,而且序列化过程中会
创建许多临时对象,容易触发垃圾回收。
Parcelable速度快,效率高,但是所需代码量大,且阅读性低。
那么速度快多少呢? 网上已经有很多人验证过,并得出了结论。Parcelable比Serializable 快了十倍左右。
我们完全可以忍受它代码量大的缺点而使用到它高效率的一面。那有什么办法来减轻大量的模板代码呢?
有的,github上有很多牛人都写好了框架,我推荐一个吧:
> https://github.com/sockeqwe/ParcelablePlease

## Android中的IPC
<table border="3">
<tr>
<td>名称</td>
<td>优点</td>
<td>缺点</td>
<td>适用场景</td>
</tr>
<tr>
<td>Bundle</td>
<td>简单易用</td>
<td>只能传输Bundle支持的数据类型</td>
<td>四大组件间的进程间通信</td>
</tr>
<tr>
<td>文件共享</td>
<td>简单易用</td>
<td>不适合高并发场景,并且无法做到进程间的即时通信</td>
<td>无并发访问情形,交换简单的数据实时性不高的场景</td>
</tr>
<tr>
<td>AIDL</td>
<td>功能强大,支持一对多并发通信,支持实时通信</td>
<td>使用稍复杂,需要处理好线程同步</td>
<td>一对多通信且有RPC需求</td>
</tr>
<tr>
<td>Messager</td>
<td>功能一般,支持一对多串行通信,支持实时通信</td>
<td>不能很好处理高并发情形,不支持RPC,数据通过Message进行传输,因此只能传输Bundle支持的类型</td>
<td>低并发的一对多即时通信,无RPC需求,或者唔需要返回结果的RPC需求</td>
</tr>
<tr>
<td>ContentProvider</td>
<td>在数据源访问方面功能强大,支持一对多并发数据共享,可通过Call方法扩展其他操作</td>
<td>可以理解为受约束的AIDL,主要提供数据源的CRUD操作</td>
<td>一对多的进程间的数据共享</td>
</tr>
<tr>
<td>Socket</td>
<td>功能强大,可以通过网络传输字节流,支持一对多并发实时通信</td>
<td>实现细节稍微有点繁琐,不支持直接的RPC</td>
<td>网络数据交换</td>
</tr>



