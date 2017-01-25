#AlarmManager

##一种系统级别的提醒服务

* 在指定的时间为我们开启相应的intent
* 一般使用PendingIntent

##PendingIntent：待定的intent
* PendingIntent 是一类并非立刻执行的Intent
* 使用场景是满足某些条件才执行的任务: Notification, AlarmManager, BroadCast
* 优势所在：自身携带了context, 不需要在特定activity才能执行. 
* 获得的方法：getActivity(), getBroadcast(), getService();     

```java
Intent intent1 = new Intent(this, upDateService.class);
PendingIntent pendingIntent = PendingIntent.getService(this, 0, intent1, 0);
```     

* 在开启新的PendingIntent之前，有必要把同类已无效的cancel掉
       
```java
manager.cancel(pendingIntent);
manager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP, triggerAtTime, pendingIntent);
```

##获取一个AlarmManager
* 并不是new一个对象，而是获取一个服务
```java
AlarmManager manager = (AlarmManager) getSystemService(ALARM_SERVICE);
```


##设置AlarmManger

```java
一次性闹钟：
set(int type，long startTime，PendingIntent pi);

固定时间重复闹钟：
setRepeating(int type，long startTime，long intervalTime，PendingIntent pi)；

非固定时间重复闹钟：
setInexactRepeating（int type，long startTime，long intervalTime，PendingIntent pi；
```


##Alarm type

```java
AlarmManager.ELAPSED_REALTIME: 表示闹钟在手机睡眠状态下不可用，该状态下闹钟使用相对时间（相对于系统启动开始），状态值为3；

AlarmManager.ELAPSED_REALTIME_WAKEUP: 表示闹钟在睡眠状态下会唤醒系统并执行提示功能，该状态下闹钟也使用相对时间，状态值为2；

AlarmManager.RTC: 表示闹钟在睡眠状态下不可用，该状态下闹钟使用绝对时间，即当前系统时间，状态值为1；

AlarmManager.RTC_WAKEUP: 表示闹钟在睡眠状态下会唤醒系统并执行提示功能，该状态下闹钟使用绝对时间，状态值为0；

AlarmManager.POWER_OFF_WAKEUP: 表示闹钟在手机关机状态下也能正常进行提示功能，所以是5个状态中用的最多的状态之一，该状态下闹钟也是用绝对时间，状态值为4；不过本状态好像受SDK版本影响，某些版本并不支持；
```


