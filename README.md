# Android-study
整理、记录Android学习计划，读书笔记等。资料来源于网络和书籍。
文章收集一些典型知识点，重点记录每个功能解决了什么问题，如果时间充裕也会探究底层原理。

**声明**

1. 文章中大部分来源于别人文章，这里只做整理和摘录，通过文中链接可以找到原文。
2. 文中不应包含工作相关内容 
### [学习Android系统]

![image](android/android.png)

- **四大组件**
    - [Activity](android/activity.md)
    - [Service](android/service.md)
    - ContentProvider
    - BroadcastReceiver
- **其他**
    - layout
    - [Intent 和 Intent 过滤器](https://developer.android.com/guide/components/intents-filters.html?hl=zh-cn#Building) : Intent 是一个消息传递对象，您可以使用它从其他应用组件请求操作
- **进程间通信机制**
    - Binder
    - 匿名共享内存
    - AIDL
- View系统、Window系统，绘制原理，事件分发
- 动画框架
- 多线程机制，消息机制 AsyncTask，Thread/Handler
- 系统启动过程，system_server启动过程
- 资源管理系统，资源加载机制等
- 开源项目
- framework学习
- JNI:[NDK Programmer's Guide](http://brian.io/android-ndk-r10c-docs/Programmers_Guide/html/index.html)
- [ Android中对象池的使用](http://blog.csdn.net/a_tao123/article/details/47137471)
- [Thread, Looper和Handler机制](https://hit-alibaba.github.io/interview/Android/basic/Android-handler-thread-looper.html): 
除了UI线程/主线程以外，普通的线程(先不提HandlerThread)是不自带Looper的。一个Looper类似一个消息泵。它本身是一个死循环，不断地从MessageQueue中提取Message或者Runnable。而Handler可以看做是一个Looper的暴露接口，向外部暴露一些事件，并暴露sendMessage()和post()函数。

### 性能优化
- [内存分析](tunning/memory.md)
- CPU分析


### Java
- **语言层面**：	《Effective Java》	《Java解惑》 [Thinking in java](https://github.com/quanke/think-in-java)
- **并发编程**：(Java Concurrency In Practice)
- **库**
	- **Collections**
	- **[concurrent](java/concurrent.md)**
- **[java.util](java/java.util.md)**
- **[Java Performance Tuning](http://www.javaperformancetuning.com/tips/synchronization.shtml#REF1)**
- **[Java 理论与实践 from IBM](https://www.ibm.com/developerworks/cn/java/j-jtp/)**
- **[Java 解惑](java/JavaPuzzlers.md)**
- **[Thread](java/thread.md)**
- **[泛型](java/generic.md)**
- **[Lambda](java/lambda.md)**
### ART

### [JVM](jvm/jvm.md)
- **虚拟机**：	 《深入理解Java虚拟机》	《实战Java虚拟机》


### 了解编译原理等CS基本知识


###  深入Linux内核


### 设计模式
- **[单例模式](design/singleton.md)**

### 工具
- [IDEA](tools/idea.md)
- [MAT](tools/mat.md)
- [DDMS](tools/ddms.md)
- [dumpsys](tools/dumpsys.md)
- [APT](tools/apt.md)
- [jprofiler](tools/jprofiler.md)
- [VisualVM](https://visualvm.github.io/download.html)
- [adb](tools/adb.md)
- [LeakCanary](tools/leakcanary.md)
- [Emmagee](emmagee.md)

### 参考资料
- [国内 Android 开发者信息](https://github.com/android-cn/android-dev-cn)
- [androidxref源码阅读](http://androidxref.com/)

### OpenSource
- [UIL](https://github.com/nostra13/Android-Universal-Image-Loader)



### 收藏夹
- [RxJava ](http://gank.io/post/560e15be2dca930e00da1083)