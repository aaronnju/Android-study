<http://blog.csdn.net/luoshengyang/article/details/6618363>

![image](http://hi.csdn.net/attachment/201107/19/0_13110996490rZN.gif)

Binder机制是采用OpenBinder演化而来，优势：<http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0319/2619.html>

1. 有更好的传输性能。对比于Linux的通信机制，
	- socket：是一个通用接口，导致其传输效率低，开销大；
	- 管道和消息队列：因为采用存储转发方式，所以至少需要拷贝2次数据，效率低；
	- 共享内存：虽然在传输时没有拷贝数据，但其控制机制复杂（比如跨进程通信时，需获取对方进程的pid，得多种机制协同操作）。
2. 采用C/S的通信模式。而在linux通信机制中，目前只有socket支持C/S的通信模式，但socket有其劣势
3. 安全性更高。Linux的IPC机制在本身的实现中，并没有安全措施，得依赖上层协议来进行安全控制。而Binder机制的 UID/PID是由Binder机制本身在内核空间添加身份标识，安全性高；并且Binder可以建立私有通道，这是linux的通信机制所无法实现的 （Linux访问的接入点是开放的）。




//// TODO 深入