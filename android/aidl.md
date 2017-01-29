<http://blog.csdn.net/singwhatiwanna/article/details/17041691>

AIDL (Android Interface Definition Language) 是一种IDL 语言，用于生成可以在Android设备上两个进程之间进行进程间通信的代码。如果在一个进程中（例如Activity）要调用另一个进程中（例如Service）对象的操作，就可以使用AIDL生成可序列化的参数。


1. 创建一个包用来存放aidl文件,在里面新建IMyService.aidl文件，还需要创建Student.aidl和Student.java。拷贝到客户端和服务端module中
2. 先建立一个android工程，用作服务端,创建服务端service，实现private final IMyService.Stub mBinder = new IMyService.Stub() { 
3. 新建一个客户端工程使用服务