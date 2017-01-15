
## [双重检查锁定失效的原因](http://www.ibm.com/developerworks/java/library/j-dcl/index.html)
经典情况下我们使用下面代码实现单例，但实际上在部分jvm上是存在隐患的。
	public static Singleton getInstance()
	{
	  if (instance == null)
	  {
	    synchronized(Singleton.class) {  //1
	      if (instance == null)          //2
	        instance = new Singleton();  //3
	    }
	  }
	  return instance;
	}

针对 Java 5.0 修订前参考了 Java 内存模型，双重检查锁定失败的问题是因为：//3行代码创建了一个 Singleton 对象并初始化变量 instance 来引用此对象。这行代码的问题是：在 Singleton 构造函数体执行之前，变量 instance 可能成为非 null 的。
	
	mem = allocate();             //Allocate memory for Singleton object.
	instance = mem;               //Note that instance is now non-null, but has not been initialized.
	ctorSingleton(instance);      //Invoke constructor for Singleton passing instance.

## 使用 static 字段的单例实现
这个同步过程由JVM实现了。

	class Singleton
	{
	  private Vector v;
	  private boolean inUse;
	  private static Singleton instance = new Singleton();
	
	  private Singleton()
	  {
	    v = new Vector();
	    inUse = true;
	    //...
	  }
	
	  public static Singleton getInstance()
	  {
	    return instance;
	  }
	}