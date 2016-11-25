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
```
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
* ** add uses-permission **
``` 
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```
