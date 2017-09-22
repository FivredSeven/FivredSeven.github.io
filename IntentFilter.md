# Intent 与 IntentFilter
## Intent
intent在开发中非常常见。可以理解为搭载数据的桥梁，主要有以下几种场景。
>startActivit(Intent intent);         
>startService(Intent intent);        
>bindService(Intent intent);            
>sendBroadcasr(Intent intent);          

Intent的调用一般都会说分为两种，一种是显式调用，另一种是隐式调用。         
Intent由6部分信息组成：Component Name、Action、Data、Category、Extras、Flags             
除第一个Component Name外，其他五个都可用于隐式调用。                              
在使用过程中，Action、Data、Category、Extras、Flags都是不同种类的过滤器，用于过滤掉不符合条件的数据。                            
例如action值为android.intent.action.SEND的就一般用于分享，所有支持action.Send的Activity都将可能被唤起选择。        
例如Data  android:mimeType="image/*"  指定MIME类型为图片                  

下面来说说两个使用IntentFilter的小例子。
