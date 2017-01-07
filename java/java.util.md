
- [Java学习笔记(类库介绍)](http://blog.csdn.net/llping2011/article/details/9952589)

- **collections** 
-
    如何遍历集合、删除集合元素
	Iterator it = collection.iterator(); // 获得一个迭代子
	    while(it.hasNext()) {
	    Object obj = it.next(); // 得到下一个元素
	}


[并发集合类](https://www.ibm.com/developerworks/cn/java/j-jtp07233/) Hashtable 和 Vector ，以及同步的包装器类 Collections.synchronizedMap 和 Collections.synchronizedList ，为 Map 和 List 提供了基本的有条件的线程安全的实现。然而，某些因素使得它们并不适用于具有高度并发性的应用程序中――它们的 集合范围的单锁特性对于可伸缩性来说是一个障碍，而且，很多时候还必须在一段较长的时间内锁定一个集合，以防止出现 ConcurrentModificationException s异常。 ConcurrentHashMap 和 CopyOnWriteArrayList 实现提供了更高的并发性，同时还保住了线程安全性，只不过在对其调用者的承诺上打了点折扣。 ConcurrentHashMap 和 CopyOnWriteArrayList 并不是在您使用 HashMap 或 ArrayList 的任何地方都一定有用，但是它们是设计用来优化某些特定的公用解决方案的。许多并发应用程序将从对它们的使用中获得好处。

- **List **
    - CopyOnWriteArrayList：在那些遍历操作大大地多于插入或移除操作的并发应用程序中，一般用 CopyOnWriteArrayList 类替代 ArrayList 。 CopyOnWriteArrayList 含有对一个不可变数组的一个可变的引用，因此，只要保留好那个引用，您就可以获得不可变的线程安全性的好处，而且不用锁 定列表。


| 类       | 内部实现 |线程安全
| -------- | -----------|--|
|ArrayList|  数组|N|
|Vector|  数组|Y|
|LinkedList|双向链表|N|


+ 线程不安全的如何解决：Collections.synchronizedList包装下 


- **Map**
    - 用作键的对象必须实现了hashcode()、equals()方法
    - Hashtable： JDK 1.0引入，线程安全，但他的所有方法都是同步的，无竞争的同步会导致可观的性能代价。
    - HashMap：JDK1.2出现的，它通过提供一个不同步的基类和一个同步的包装器 Collections.synchronizedMap ，解决了线程安全性问题。 通过将基本的功能从线程安全性中分离开来， Collections.synchronizedMap 允许需要同步的用户可以拥有同步，而不需要同步的用户则不必为同步付出代价。
    - Hashtable、Collections.synchronizedMap不足：
        - 伸缩性差，因为一次只能有一个线程可以访问hash表。如果hash不合理，即使是简单的get也会很耗时。
        - 混合操作需要额外的同步，例如迭代（ad-hoc iteration(contains race conditions)，normal iteration (can throw ConcurrentModificationException)）或者put-if-absent（空则放入）。此时需要额外锁住整个map，弊端是会阻塞其他线程。
    - [ConcurrentHashMap](ConcurrentHashMap.md): JDK 1.5，一定程度上支持并发读、并发写。在任何不依赖于锁整个表来防止更新的应用程序中，可以使用 ConcurrentHashMap 来替代 synchronizedMap 或 Hashtable 。
    - Tips:
        - 如果在一个线程正在通过一个 Iterator 遍历集合时，另一个线程也来修改这个 集合，那么接下来的 Iterator.hasNext() 或 Iterator.next() 调用将抛出 ConcurrentModificationException 异常
        
| 类       | Key null|value null|基类 |线程安全 |
| -------- | -----------|--|
| Hashtable | N | N| Dictionary | 线程安全|
|ConcurrentHashMap|N|N| AbstractMap| 线程局部安全|
|TreeMap|N|Y|AbstractMap |线程不安全|
|HashMap|Y|Y|AbstractMap |线程不安全|


- synchronizedMap 和 synchronizedList ，有时也被称作 有条件地线程安全――所有 单个的操作都是线程安全的，put-if-absent（空则放入），需要外部的同步


**参考资料**

[Java 理论与实践: 并发集合类](http://www.ibm.com/developerworks/cn/java/j-jtp07233/)