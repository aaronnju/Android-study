
## 基本概念

![image](http://img.blog.csdn.net/20160713193502676?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


||	TCP	|UDP|
|:-----:|:--------|:-------|
| 是否连接	| 面向连接	| 面向非连接 |
| 传入可靠性| 	可靠地	| 不可靠的|
| 引用场景	| 传输大量数据	| 传输小量数据|
| 速度	| 慢	| 快 |
| 举例说明	| 银行，	| windows的ping命令 QQ发消息


**socket:** 
为了实现以上的通信过程而建立成来的通信管道，其真实的代表是客户端和服务器端的一个通信进程，双方进程通过socket进行通信，而通信的规则采用指定的协议。socket只是一种连接模式，不是协议,

## HTTP概念
1. Get和Post的对比  
	- GET：在请求的URL地址后以?的形式带上交给服务器的数据，多个数据之间以&进行分隔，但数据容量通常不能超过2K
	- POST: 在请求体中向服务器发送数据，传输没有数量限制

2. Http状态码合集  
	- 100~199 : 成功接受请求，客户端需提交下一次请求才能完成整个处理过程
	- 200: OK，客户端请求成功
	- 300~399：请求资源已移到新的地址(302,307,304)
	- 401：请求未授权，改状态代码需与WWW-Authenticate报头域一起使用
	- 403：Forbidden，服务器收到请求，但是拒绝提供服务
	- 404：Not Found，请求资源不存在
	- 500：Internal Server Error，服务器发生不可预期的错误
	- 503：Server Unavailable，服务器当前不能处理客户端请求，一段时间后可能恢复正常

3. socket连接和http连接的区别  
TCP/IP是传输层协议，主要解决数据如何在网络中传输；而HTTP是应用层协议，主要解决如何包装数据。Socket是对TCP/IP协议的封装，Socket本身并不是协议，而是一个调用接口（API），通过Socket，我们才能使用TCP/IP协议。  
	- http连接：HTTP连接使用的是“请求—响应”的方式。http连接就是所谓的短连接，即客户端向服务器端发送一次请求，服务器端响应后连接即会断掉；
	- socket连接：通常情况下Socket连接就是TCP连接。socket连接就是所谓的长连接，理论上客户端和服务器端一旦建立起连接将不会主动断掉；但是由于各种环境因素可能会是连接断开，比如说：服务器端或客户端主机down了，网络故障

4.  Http和Socket使用场景选择  
	视频，图片，断点续传的情况下要用socket  
	移动互联网一般做接口方面都是采用HTTP接口

## HTTP库

- 在Android 2.2版本之前，HttpClient（ Apache HTTP Client）拥有较少的bug，因此使用它是最好的选择。
- 在Android 2.3版本及以后，HttpURLConnection则是最佳的选择。它的API简单，体积较小。压缩和缓存机制可以有效地减少网络访问的流量，在提升速度和省电方面也起到了较大的作用。

需要考虑的几方面：<https://segmentfault.com/a/1190000003965158>    

- 能够取消现有的网络请求
- 能够并发请求
- 连接池能够复用存在的Socket连接
- 本地对于响应的缓存
- 简单的异步接口来避免主线程阻塞
- 对于REST API的封装
- 重连策略
- 能够有效地载入与传输图片
- 支持对于JSON的序列化
- 支持SPDY、HTTP/2