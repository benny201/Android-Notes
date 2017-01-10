# 运行时权限

## 静态加入权限
* 有一些功能如果不声明权限会崩溃
* 如在BroadCast的下线project中，在AndroidManiFest.XML中声明了一下permission

```java
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/> 
```     
     
* 会导致一些应用滥用权限，所以衍生了运行时权限，在使用APP的过程中确定是否使用该权限

## Two kinds of Permissions
* 1. Normal Permissions 普通权限
* 2. Dangerous Permissions 危险权限
* the system will ask for request for dangerous permission but not Normal Permissions
* the system just tells the user what permission groups the app needs, not the individual permissions.     
     
## 运行时权限实战
* []()