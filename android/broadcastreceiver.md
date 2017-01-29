<http://blog.csdn.net/zuolongsnail/article/details/6450156>


## 概述
1. 广播接收器是一个专注于接收广播通知信息，并做出对应处理的组件。很多广播是源自于系统代码的──比如，通知时区改变、电池电量低、拍摄了一张照片或者用户改变了语言选项。应用程序也可以进行广播──比如说，通知其它应用程序一些数据下载完成并处于可用状态。
2. 应用程序可以拥有任意数量的广播接收器以对所有它感兴趣的通知信息予以响应。所有的接收器均继承自BroadcastReceiver基类。
3. 广播接收器没有用户界面。然而，它们可以启动一个activity来响应它们收到的信息，或者用NotificationManager来通知用户。通知可以用很多种方式来吸引用户的注意力──闪动背灯、震动、播放声音等等。一般来说是在状态栏上放一个持久的图标，用户可以打开它并获取消息。

Android中的广播事件有两种

-  一种就是系统广播事件，比如：ACTION_BOOT_COMPLETED（系统启动完成后触发），ACTION_TIME_CHANGED（系统时间改变时触发），ACTION_BATTERY_LOW（电量低时触发）等等。
- 另外一种是我们自定义的广播事件。


## 使用
1. 注册广播事件：注册方式有两种，
	- 一种是静态注册，就是在AndroidManifest.xml文件中定义，注册的广播接收器必须要继承BroadcastReceiver；
	- 另一种是动态注册，是在程序中使用Context.registerReceiver注册，注册的广播接收器相当于一个匿名类。两种方式都需要IntentFIlter。
2. 发送广播事件：通过Context.sendBroadcast来发送，由Intent来传递注册时用到的Action。
3. 接收广播事件：当发送的广播被接收器监听到后，会调用它的onReceive()方法，并将包含消息的Intent对象传给它。onReceive中代码的执行**时间不要超过5s，否则ANR**