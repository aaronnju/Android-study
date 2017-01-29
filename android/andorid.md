#Android


## Android系统架构
![image](android.png)

自底向上包含5大部分:Linux Kernel 、Libraries、Android Runtime、Application framework、application
         
- **Linux Kernel** android是基于linux2.6 提供核心系统服务，例如:安全、内存管理、进程管理、网络堆栈、驱动模型。
- **Android Runtime** 编程语言核心类库 + Dalvik虚拟机(ART)
- **Libraries(运行库)**   C/C++支持
	- Bionic 系统C库: C语言标准库，系统最底层的库，C库基由Linux系统来调用。
	- MediaFramework: Android系统多媒体库，基于PackerVideo的OpenCORE,支持各种音频、视频格式的录制和播放,包括静态图片文件。
	- LibVebCore:web浏览器引擎,驱动Android浏览器和内嵌的web视图
	- SGL:基本的2D图形引擎
	- 3D: 基于OpenGL ES的实现。
	- SQLite:应用程序使用的强大、轻量的关系数据库引擎。
- **Application Framework** 开发者接触的最多的部分
	- View 用于构建应用的视图集合。包括包括列表、网格、文本框、按钮等
	- Content Providers 使应用程序能访问其他应用程序（如通讯录）的数据，或共享自己的数据。
	- Resource Manager 提供访问非代码资源，如本地化字符串、图形和布局文件。
	- Notification Manager 在状态栏显示自定义警告。
	- Activity Manager 管理应用程序生命周期
- **Applications** Android系统核心app集合，包括电子邮件客户端、SMS、日期、地图、浏览器等。
 