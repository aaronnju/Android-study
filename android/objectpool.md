对象池可以看做一种空间换时间的策略。可以看作是减少GC压力的首选方法，同时也是最简单的方法。

## Message Pool
1. Message使用链表数据结构，通过静态sPool保存缓存的Message对象，链表通过next变量实现。
2. 使用的时候通过Message.obtain()从Message Pool中获取Messages实例，如果链表空就创建一个新对象，创建的时候不做缓存处理。
3. Message用完之后，使用recycle()进行释放，释放的时候会把对象加入到上面提到的pool中，但Message对象比较特殊，Looper类自动对其进行了回收，[msg.recycleUnchecked()](http://androidxref.com/6.0.1_r10/xref/frameworks/base/core/java/android/os/Looper.java#165)，而recycleUnchecked不会判断是否已经被缓存，如果用户自己提前调用了recycle，将会引发链表错误。
4. 通过MAX_POOL_SIZE 限制pool的大小，超过这个限制的对象不做缓存了。

Message源码  
<http://androidxref.com/6.0.1_r10/xref/frameworks/base/core/java/android/os/Message.java#sPoolSync>

	public final class Message implements Parcelable {
	    /*package*/ static final int FLAG_IN_USE = 1 << 0;
	    int flags;
	    Message next;
	    private static final Object sPoolSync = new Object();
	    private static Message sPool;
	    private static int sPoolSize = 0;
	    private static final int MAX_POOL_SIZE = 50;
	    private static boolean gCheckRecycle = true;

	    public static Message obtain() {
	        synchronized (sPoolSync) {
	            if (sPool != null) {
	                Message m = sPool;
	                sPool = m.next;
	                m.next = null;
	                m.flags = 0; // clear in-use flag
	                sPoolSize--;
	                return m;
	            }
	        }
	        return new Message();
	    }
	
	    public void recycle() {
	        if (isInUse()) {
	            if (gCheckRecycle) {
	                throw new IllegalStateException("This message cannot be recycled because it "
	                        + "is still in use.");
	            }
	            return;
	        }
	        recycleUnchecked();
	    }
	
	    void recycleUnchecked() {
	        // Mark the message as in use while it remains in the recycled object pool.
	        // Clear out all other details.
	        flags = FLAG_IN_USE;
	        what = 0;
	        arg1 = 0;
	        arg2 = 0;
	        obj = null;
	        replyTo = null;
	        sendingUid = -1;
	        when = 0;
	        target = null;
	        callback = null;
	        data = null;
	
	        synchronized (sPoolSync) {
	            if (sPoolSize < MAX_POOL_SIZE) {
	                next = sPool;
	                sPool = this;
	                sPoolSize++;
	            }
	        }
	    }
	    /*package*/ boolean isInUse() {
	        return ((flags & FLAG_IN_USE) == FLAG_IN_USE);
	    }
	}

## android.support.v4.util.pools
Android系统系统了Pool接口, SimplePool类与SynchronizedPool类用于对象缓存，他们内部使用一个固定大小的Object数组实现，其中SynchronizedPool仅仅是增加一层同步。

下面是使用示例，也可以参考[VelocityTracker](http://androidxref.com/6.0.1_r10/xref/frameworks/base/core/java/android/view/VelocityTracker.java)，需要注意的是，obtain的时候会返回null，此时要用户自己创建对象。

	public class MyPooledClass {
	    private static final SynchronizedPool<MyPooledClass> sPool =
	            new SynchronizedPool<MyPooledClass>(10);
	    public static MyPooledClass obtain() {
	        MyPooledClass instance = sPool.acquire();
	        return (instance != null) ? instance : new MyPooledClass();
	    }
	    public void recycle() {
	         // Clear state if needed.
	         sPool.release(this);
	    }
	}