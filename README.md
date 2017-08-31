# FivredSeven.github.io
57's blog
tracywu

- [ ] [Activity的启动模式](./Activity的启动模式.md)
- [ ] [IntentFilter](./IntentFilter.md)

# Android Activity生命周期
## 官方镇宝图
![](http://android.xsoftlab.net/images/activity_lifecycle.png)   
我相信这张图，或者说类似的生命周期图，大家已经看了不下百遍也有数十遍，那正常流程下的生命周期顺序就不说了。   
>FragmentActivity和Activity有一点不一样的是，FragmentActivity在打开另一个FragmentActivity的时候，前面的FragmentActivity只会走onPause，不会走onStop。



我们来聊聊一些特殊的场景吧。
#### 1.被系统杀死的场景
首先要明白一点，android系统，任何app都可能因为系统内存不足等原因而被系统杀掉，只不过不同app有不同的策略来应对这种场景。     
app在前台与用户交互的时候一般是不会被系统杀掉的，通常是home键后，程序在后台的时候，才会被杀。这时候我们来分析下生命周期变化。   
用户按下Home键 __activity--- onPause->onSaveInstanceState->onStop__        
onSaveInstanceState是紧跟着onPause调用的，可以理解为，当页面不在前台的时候，系统就认为这个activity有被杀死的可能，这时候就要保存数据了。所以我们通常把数据保存放在onSaveInstanceState。      
这时候如果系统杀掉我们的app，是不会走任何生命周期的。用户又触发打开我们app的时候，生命周期是这样的     
__onCreate->onStart->onRestoreInstanceState->onResume__                
这里的onCreate是带了之前我们在onSaveInstanceState保存的bundle值的。                 

>我有在书籍中看到说，onSaveInstanceState和onRestoreInstanceState 系统自动为我们做了一定的恢复工作，比如编辑框输入的文字，listview滑动的位置。我特意去验证看看，发现系统并没有这么智能，只会正常走生命周期而已，我用的是7.0系统。 如果我的结论有误，还请为我指出。             


我在网上找到一个非常有干货的博文,而且写了五篇一个系列详细讲述了APP被杀死的前因后果。        
[http://www.jianshu.com/p/00fef8872b68](http://www.jianshu.com/p/00fef8872b68)   


有几点要注意的是        
>AMR ActivityManagerService         
>LMK LowMemoryKiller

#### 2.开发者模式设置为不保留活动的场景
这种场景当然是比较少见的，之前我也一直没留意，但是我们的测试工程师最近在测试的时候不知为何把这种场景写进了标准用例里。          
那我们就来看看遇到这种场景，activity的生命周期是如何变化的。          
activityA打开activityB。正常来说A会走 __onPause->onSaveInstanceState->onStop__        
但是当你打开不保留活动的时候A走完onStop还会继续走onDestroy。         
看上去这个页面被销毁了，这时候在activityB，点击返回按钮，A却活过来了          
activityA: __onCreate->onStart->onRestoreInstanceState->onResume__          
这是为什么呢。经过查阅资料发现，finish()方法里有将activity移出栈，所以只要没有显示的调用activityA的finish()方法，虽然activityA执行了onDestroy，但Activity栈中依然保留有activityA         



#### 3.横竖屏切换的场景
__onConfigurationChanged->onPause->onSaveInstanceState->onStop->onDestroy->onCreate->onStart->onRestoreInstanceState->onResume__            
不论是横屏切竖屏还是竖屏切横屏，生命周期都会重新走一遍。之前的版本横屏切竖屏会走两遍。            
不论一遍还是两遍，显然这都不是我们想要的，不能因为用户转换屏幕而导致流程重走一次。解决办法也很简单             
在manifest中设置该Activity的configChanges为android:configChanges="screenSize|keyboardHidden|orientation"。           
这时候生命周期就只会走onConfigurationChanged了。           
以下别人的总结，引用一下：        
1、不设置Activity的android:configChanges时，切屏会重新调用各个生命周期，切横屏时会执行一次，切竖屏时会执行两次         
2、设置Activity的android:configChanges="orientation"时，切屏还是会重新调用各个生命周期，切横、竖屏时只会执行一次         
3、设置Activity的android:configChanges="orientation|keyboardHidden"时，切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法

但是我发现我的手机设置了android:configChanges="orientation|keyboardHidden"后，横竖屏切换依然会重走生命周期。               
继续查证发现在AndroidManifest.xml里设置的MiniSdkVersion和TargetSdkVersion属性大于等于13的情况下，如果你想阻止程序在运行时重新加载Activity，除了设置"orientation"， 你还必须设置"ScreenSize"             


## onActivityResult()
这里为什么要记录onActivityResult()呢，这个生命周期看似简单，但最近让我碰上确实还费了些头脑。                 

正常流程，在使用startActivityForResult()启动一个Activity后，需要回传一些数据给前一个Activity的时候执行setResult，那么就会进入前一个Activity的onActivityResult().                   

那么我最近遇到的是在Fragment里通过ViewPager add的子Fragment，子Fragment执行startActivityForResult(),回传数据，走不到onActivityResult();          
解决方法：                  
1、在子fragment传值时，将startActivityResult()改为，getParentFragment().startActivityForResult();                         
2、在父fragment中重写onActivityResult。

    @Override  
    public void onActivityResult(int requestCode, int resultCode, Intent data) {  
        super.onActivityResult(requestCode, resultCode, data);  
  
        List<Fragment> fragments = getChildFragmentManager().getFragments();  
        if (fragments != null) {  
            for (Fragment fragment : fragments) {  
                if(fragment==null)  
                continue;  
                fragment.onActivityResult(requestCode, resultCode, data);  
            }  
        }
   }
   
   
好了，生命周期就先记录到这里。

