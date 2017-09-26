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

## 例1、从外部浏览器、从其他app等外部跳到我们的app
### 首先，编写一个html页面

    <html>
      <head>
          <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
          <title>我的网页</title>
      </head>
      <body>
          <a href="http://wuhq.com/req?param0=0&param1=1">启动我的app</a>
      </body>
    </html>
                    
           
### 接着在AndroidManifest里给需要接收的Activity里的intent-filter中加入如下元素： 
    <intent-filter>
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
      <category android:name="android.intent.category.BROWSABLE" />

      <data
        android:host="wuhq.com" 
        android:scheme="http" />
    </intent-filter>

### 最后在接收的Activity里解析数据
    Uri uri = getIntent().getData();  
    String param1 = uri.getQueryParameter("param1");  
    String param0 = uri.getQueryParameter("param0");
    
    
## 例2、从外部打开文件选择打开方式，如果我们app支持该文件类型，则会显示出来
### 首先在AndroidManifest里给需要接收的Activity里的intent-filter里加入下列元素
    <action android:name="android.intent.action.SEND" />
    <category android:name="android.intent.category.DEFAULT" />
    <data android:mimeType="image/*" />
    <data android:mimeType="text/*" />  

### 接着在Activity里接收数据， 那么在外部打开文本或者图片的时候就可以选择我们自己的app了
    void onCreate (Bundle savedInstanceState) {
        ...
        // Get intent, action and MIME type
        Intent intent = getIntent();
        String action = intent.getAction();
        String type = intent.getType();

        if (Intent.ACTION_SEND.equals(action) && type != null) {
            if (text/plain.equals(type)) {
                handleSendText(intent); // Handle text being sent
            } else if (type.startsWith(image/)) {
                handleSendImage(intent); // Handle single image being sent
            }
        } else if (Intent.ACTION_SEND_MULTIPLE.equals(action) && type != null) {
            if (type.startsWith(image/)) {
                handleSendMultipleImages(intent); // Handle multiple images being sent
            }
        } else {
            // Handle other intents, such as being started from the home screen
        }
        ...
    }
