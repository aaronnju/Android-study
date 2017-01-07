[Java 理论与实践: 构建一个更好的 HashMap](http://www.ibm.com/developerworks/cn/java/j-jtp08223/)

[深入浅出ConcurrentHashMap（1.8）](http://www.jianshu.com/p/c0642afe03e0)

1.8的实现已经抛弃了Segment分段锁机制，利用CAS+Synchronized来保证并发更新的安全，底层依然采用数组+链表+红黑树的存储结构。