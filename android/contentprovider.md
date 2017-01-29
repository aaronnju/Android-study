## 概述
ContentProvider一般为存储和获取数据提供统一的接口，可以在不同的应用程序之间共享数据。


应用程序间传输数据

- Binder进程间通信机制，不同的应用程序之间可以通过Binder进程间调用来传输数据
- 如果在进程间传输大量的数据，效率是不是会很低下呢？这时候，前面我们在Android系统匿名共享内存Ashmem（Anonymous Shared Memory）


一个大型的应用程序软件架构:
![image](http://hi.csdn.net/attachment/201111/7/0_1320687133YFYN.gif)
ref:
[ Android应用程序组件Content Provider简要介绍和学习计划](http://blog.csdn.net/luoshengyang/article/details/6946067)

## 适用场景 
<http://www.cnblogs.com/devinzhang/archive/2012/01/20/2327863.html>


1) ContentProvider为存储和读取数据提供了统一的接口 

2) 使用ContentProvider，应用程序可以实现数据共享

3) android内置的许多数据都是使用ContentProvider形式，供开发者调用的(如视频，音频，图片，通讯录等)

## 使用方法
1. 定义好URI，ContentResolver用此区别不同的ContentProvider，格式如下：

	![image](http://upload-images.jianshu.io/upload_images/1362430-b39bc91ec8e272af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 创建一个类继承ContentProvider。默认该Provider需要实现如下六个方法，
	- onCreate(), 
	- query(Uri, String[], String, String[], String),
	- insert(Uri, ContentValues), 
	- update(Uri, ContentValues, String, String[]), 
	- delete(Uri, String, String[]),  
	- getType(Uri)
	
	所有访问都提供URI进行识别，函数实现过程中可以使用UriMatcher来判断是否是自己支持的URI

3. 在AndroidManifest.xml中注册
	> <provider
	> android:authorities="xxx.xxxx.xxx"  
	> android:name=".provider.TestProvider" />
		
		其他重要标签：
		- android:exported 设置此provider是否可以被其他应用使用。
		- android:readPermission 该provider的读权限的标识
		- android:writePermission 该provider的写权限标识
		- android:permission provider读写权限标识
		- android:grantUriPermissions 临时权限标识，true时，意味着该provider下所有数据均可被临时使用；false时，则反之

4. 其他应用掉用ContentResolver来获取对应的数据