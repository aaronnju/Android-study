
<https://hit-alibaba.github.io/interview/Android/basic/Android-handler-thread-looper.html>


除了UI线程/主线程以外，普通的线程(先不提HandlerThread)是不自带Looper的。一个Looper类似一个消息泵。它本身是一个死循环，不断地从MessageQueue中提取Message或者Runnable。而Handler可以看做是一个Looper的暴露接口，向外部暴露一些事件，并暴露sendMessage()和post()函数。