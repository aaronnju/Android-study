# Android-study
整理、记录Android学习计划，读书笔记等。资料来源于网络和书籍。
文章收集一些典型知识点，重点记录每个功能解决了什么问题，如果时间充裕也会探究底层原理。

**声明**

1. 文章中大部分来源于别人文章，这里只做整理和摘录，通过文中链接可以找到原文。
2. 文中不包含工作相关内容 

## [Android](android/android.md)


- **四大组件**
    - [Activity](android/activity.md)
    - [Service](android/service.md)
    - [ContentProvider](android/contentprovider.md)
    - [BroadcastReceiver](broadcastreceiver.md)
- **其他**
    - [layout](android/layout.md)
    - [Intent 和 Intent 过滤器](https://developer.android.com/guide/components/intents-filters.html?hl=zh-cn#Building) : Intent 是一个消息传递对象，您可以使用它从其他应用组件请求操作
    - [对象缓存](android/objectpool.md)  
    
- **进程间通信机制**
    - [Binder](android/binder.md)
    - 匿名共享内存
    - [AIDL](android/aidl.md)
    
- **Thread**
    - [Handler](android/handler.md)
    
- View系统、Window系统，绘制原理，事件分发
- 动画框架
- 多线程机制，消息机制 AsyncTask，Thread/Handler
- 系统启动过程，system_server启动过程
- 资源管理系统，资源加载机制等
- [网络请求](android/network.md)
- framework学习
- JNI:[NDK Programmer's Guide](http://brian.io/android-ndk-r10c-docs/Programmers_Guide/html/index.html)
- [ Android中对象池的使用](http://blog.csdn.net/a_tao123/article/details/47137471)
- 工程化：Crash分析、日志回传、自动编译、发包
- gradle

## 性能优化
- [内存分析](tunning/memory.md)
- CPU分析

## ART

## Java


- **语言层面**：	《Effective Java》	 [Thinking in java](https://github.com/quanke/think-in-java)
- **并发编程**：(Java Concurrency In Practice)

- [java.util](java/java.util.md)
- [Java Performance Tuning](http://www.javaperformancetuning.com/tips/synchronization.shtml#REF1)
- [Java 理论与实践 from IBM](https://www.ibm.com/developerworks/cn/java/j-jtp/)
- [Java 解惑](java/JavaPuzzlers.md)
- [Thread](java/thread.md)
- [泛型](java/generic.md)
- [Lambda](java/lambda.md)

## 技术专题
- [Reflec](tech/reflect.md)

### 库
- [java.util.Collections](java/collections.md)
- [java.util.concurrent.*](java/concurrent.md)


## JVM
- [基础知识](jvm/jvm.md)
- [深入理解java虚拟机](jvm/understandjvm.md)
- - **虚拟机**：	 《深入理解Java虚拟机》	《实战Java虚拟机》


### 了解编译原理等CS基本知识


###  深入Linux内核


## 设计模式
- **[单例模式](design/singleton.md)**
- **[Android源码设计模式解析与实战](https://github.com/simple-android-framework/android_design_patterns_analysis)**

## 工具
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
- [Fiddler](tools/fiddler.md)
- [反编译](tools/decompile.md)

## 参考资料
- [国内 Android 开发者信息](https://github.com/android-cn/android-dev-cn)
- [androidxref源码阅读](http://androidxref.com/)

## 阅读资料
- [Java 理论与实践](http://www.ibm.com/developerworks/cn/java/j-jtp/) ：此系列文章略老，但行文甚好，难得。

## OpenSource
- 图像
	- [UIL](https://github.com/nostra13/Android-Universal-Image-Loader)
	- [picasso](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/0731/1639.html)
- [RxJava](opensource/rxjava.md)

## 收藏夹
- [RxJava ](http://gank.io/post/560e15be2dca930e00da1083)


## 读书写字模式

- IBM 系列文章
http://www.ibm.com/developerworks/cn/java/j-jtp06197.html

[正确使用 volatile 的模式], 能够把使用场景提取成“模式”，并举例说明。能很**有条例**的向读者说明此功能的用法