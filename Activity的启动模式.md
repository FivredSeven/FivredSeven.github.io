# Android Activity的启动模式之taskAffinity

启动模式太基础不过了，但是基础的东西也会有些偏门特性，我们稍后来讲这特殊的taskAffinity      

启动模式有四种             
standard、singleTop、singleTask、singleInstance  

## standard 默认模式
每次启动一个Activity，都会创建一个新的实例，该Activity属于启动它的Activity的任务栈中
## singleTop 栈顶复用模式
每次启动一个Activity，都会去检测栈顶是否为该Activity         
如果是，则复用该Activity，走它的onNewIntent()方法            
如果不是，则创建一个新的实例，类似standard模式
## singleTask 栈内复用模式
每次启动一个Activity，都会去检测当前栈内是否存在该Activity            
如果存在，则复用该Activity,走它的onNewIntent()方法，并且把栈内该Activity以上的activity全部移出栈,走onDestroy()
如果不存在，则创建一个新的实例，类似standard模式
## singleInstance 全局唯一模式
该模式具备singleTask模式的所有特性外，与它的区别就是，这种模式下的Activity会单独占用一个Task栈，具有全局唯一性，即整个系统中就这么一个实例，是单例模式，由于栈内复用的特性，后续的请求均不会创建新的Activity实例，除非这个特殊的任务栈被销毁了。                      
>根据不同的需求，选择不同的启动模式


## 好了，我们再来说说这个taskAffinity
taskAffinity是什么？可以理解为Activity所在的栈。                          
指定相同或者不指定taskAffinity，那么Activity都会在同一个栈内，如果指定了特殊的taskAffinity，那么这个Activity将和其他Activity不在同一栈内。
>Activity没指定taskAffinity的情况下，去application里找，如果也没指定则taskAffinity是app的包名>




taskAffinity有什么用呢？需求场景可能比较少，可以用于在不同app间跳转的场景里。                                     

比如：A、B两个应用，各有1，2，3三个页面，先启动A，依次进入1，2，3.                         

这时候再启动B，也依次进入1，2，3，这时候需要从B的3跳到A的3。                           

如果不指定taskAffinity，则从B3跳到A3后，用户操作返回按钮会回到A2,再返回会到A1,再返回就回到桌面了。显然我们希望从B3跳到A3后，返回能回到B3。      

这时候我们就需要把A3这个Activity的taskAffinity设置成和B3一致。这样从B3到A3，相当于是把A3这个Activity放置到了B3所在Activity栈的栈顶，这样就能达到我们想要的效果。                          

可能会有人问，真实用户怎么会需要这种场景呢。比如你有一个活动页面，用H5做的，分享到了微信朋友圈，用户看到活动介绍以后可能想报名参加，然后需要跳回你自己的app去读取一些用户信息，填一些表格然后报名。这时候如果你使用这个taskAffinity，那么跳到自己app填完资料报完名，用户点击返回的话就能回到微信了。当然，如果你压根没有启动你自己的app，而从微信跳过去那也不会有问题，如果已经打开过你的app，而且已经有打开过一系列Activity，那你显然不希望从微信跳过来以后毁坏你的Activity栈，而又不能及时回到微信。                                   

当然这个场景还适用于你们公司有开发多个app，而又有一些特殊页面需要共同使用的情况。那么taskAffinity就能很好的满足你的需要。                   

## taskAffinity还有两个点需要注意                 
1、最好和android:excludeFromRecents=”true” 这个属性配合使用。如果不配合使用那么当Actiity跳转到不同taskAffinity的Activity时，在系统管理器上会显示两个你的应用程序，这样就好像打开了两个相同app，用户会感到疑惑。                           

2、还有个点需要注意就是当你有1、2、3三个页面，1和3的taskAffinity相同，与2不同，那么你依次进入1、2、3后，按返回键回到的是1，再返回会到2。其实很好理解，1和3处于同一栈，3位于栈顶，第一次按返回键，3出栈，显示1，再返回就回到另一个栈里的2了。
