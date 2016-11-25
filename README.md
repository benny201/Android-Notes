### Android-Notes


Chapter 5 Broadcast

1.how to receive a Connectivity-change
* create Intentfilter -> addAction(CONNECTIVITY-CHange)
* create Networkchange class extends BroadReceiver 
* registerReceiver
* call unregisterReceiver in onDestory()


2.connectivity－change with connection state
* modify codes in onReceive();
* create ConnectivityManager and call **Context.getSystemService()** to get service state
<?php
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
?>
