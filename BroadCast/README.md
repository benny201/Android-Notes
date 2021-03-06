# Broadcastreceiver
> update:2016/11/24



## Create a dynamic broadcastreceiver

### 1.how to receive a Connectivity-change
* create Intentfilter -> addAction(CONNECTIVITY-CHange)
* create Networkchange class extends BroadReceiver 
* registerReceiver
* call unregisterReceiver in onDestory()
***

### 2.connectivity－change with connection state
* modify codes in onReceive();
* create ConnectivityManager and call **Context.getSystemService()** to get service state
```java
public void onReceive(Context context, Intent intent) {
        ConnectivityManager connectivityManager = (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);

        NetworkInfo networkInfo = connectivityManager.getActiveNetworkInfo();

        if(networkInfo!=null&&networkInfo.isAvailable()) {
            Toast.makeText(context,"available",Toast.LENGTH_LONG).show();
        } else {
            Toast.makeText(context,"unavailable",Toast.LENGTH_LONG).show();
        }
        
    }
```
* differences between uses-permission and permission :
``` java
两者之间的不同之一就是，作用域不同：
在manifest.xml文件中，<uses-permission>是和<application>同级的节点，一般<uses-permission >是在</application>后面的。
但<permission>就不同了，是定义在<application>和</application>之间，和Activity、Service同级别的。
其二：
<uses-permission>是官方定义的权限，是调用别人的东西的时候自己需要声明的权限。
<permission>是自己定义的权限，就是别人调用这个程序时需要用<uses-permission>来声明。
在一般情况下实际上不需要为自己的应用程序声明某个权限，除非你提供了供其他应用程序调用的代码或者数据。
这个时候你才需要使用<permission>这个标签，很显然这个标签可以让我们声明自己程序的权限
``` 
** add uses-permission **
``` 
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```


***
## create a static broadcastreceiver

why call it static: it can receive a broadcast when the app don't run
* create a new class for broadcast 
e.g.

```java
public class BootCompletedReceiver extends BroadcastReceiver{
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context,"bootcompleted",Toast.LENGTH_LONG).show();
    }
}
```
   **不要在onReceive中处理太多逻辑！！！不然会导致崩溃**

* add a <receiver> tag in AndroidManifest.XML
```java
<receiver android:name=".BootCompletedReceiver">
        <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        </intent-filter>
</receiver>
```
***     
        
## self-defined broadcast

### Normal broadcasts 标准广播

* First, create a receiver class
there must be a receiver to receive your self-defined broadcasts.

* crate a <receiver> tag for new receiver
```java
<receiver android:name=".MyBroadCastReceiver">
            <intent-filter>
                <action android:name="com.example.benny.broadcasttest.MY_CAST"/>
            </intent-filter>
</receiver>
```
* create a intent to send broadcast
```java
Intent intent = new Intent("com.example.benny.broadcasttest.MY_CAST");
sendBroadcast(intent);
```

### Ordered broadcasts 有序广播

#### Only change a function of normal broadcast

```java
sendBroadcast(intent) -> ndOrderedBroadcast(intent,null)

```

* add  
```java
android:priority=100  
```
to intent-filter of the <receiver> tag to define which receiver can receive the broadcast first.


* another function is 
```java
abortBroadcast() 
```
which can make a receiver abort a broadcast after receiving it.


***

## Locol BroadCast 本地广播

* 考虑到全局广播的安全性问题
* create a LocalReceiver
* create a LocalBroadcastManager and get an instance
* create a intentfilter
* call <localBroadcastManager.registerReceiver> to register the receiver
* unregister in Ondestory()

Code as fllow:
```java
 @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button);
        localBroadcastManager  = LocalBroadcastManager.getInstance(this);

        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent("com.example.benny.broadcastforordered.LOCAL_BROADCAST");
                localBroadcastManager.sendBroadcast(intent);
            }
        });

        intentFilter = new IntentFilter();
        intentFilter.addAction("com.example.benny.broadcastforordered.LOCAL_BROADCAST");
        localReceiver = new LocalReceiver();
        localBroadcastManager.registerReceiver(localReceiver,intentFilter);

    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        localBroadcastManager.unregisterReceiver(localReceiver);
    }
}

class LocalReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context,"LOCAL_RECEIVED!",Toast.LENGTH_LONG).show();
    }
}
```

### There is a broadcast test project for OffLine function in AndroidTestProjects repository.
> [BroadCastForOffLine](https://github.com/benny201/AndroidTestProjects/tree/master/BroadCastForOffLine "BroadCastForOffLine")
> bugs：
* android.app.AlertDialog 和 android.support.v7.app.AlertDialog 包的区别。
* > [Solution 1: 加入APPCompt主题](https://www.v2ex.com/t/292061) 
* > [Solution 2: replace android.support.v7.app.AlertDialog to android.app.AlertDialog]   