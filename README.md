### Android-Notes


Chapter 5 Broadcast

1.how to receive a Connectivity-change
* create Intentfilter -> addAction(CONNECTIVITY-CHange)
* create class extends BroadReceiver 
* registerReceiver
* call unregisterReceiver in onDestory()
