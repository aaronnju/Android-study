# java.util.concurrent

## Volatile
[Java 理论与实践: 正确使用 Volatile 变量](http://www.ibm.com/developerworks/cn/java/j-jtp06197.html)

锁提供了两种主要特性：互斥（mutual exclusion） 和可见性（visibility）。  
Volatile 变量具有 synchronized 的可见性特性，但是不具备原子特性。这就是说线程能够自动发现 volatile 变量的最新值。  
与锁相比，Volatile 变量是一种非常简单但同时又非常脆弱的同步机制，它在某些情况下将提供优于锁的性能和伸缩性。如果严格遵循 volatile 的使用条件 —— 即变量真正独立于其他变量和自己以前的值 —— 在某些情况下可以使用 volatile 代替 synchronized 来简化代码。然而，使用 volatile 的代码往往比使用锁的代码更加容易出错。  

**正确使用 volatile 的模式**  

- 模式 #1：状态标志 

		volatile boolean shutdownRequested;
		
		...
		
		public void shutdown() { shutdownRequested = true; }// 在另一个线程中调用 shutdown() 方法 
		
		public void doWork() { 
		    while (!shutdownRequested) { 
		        // do stuff
		    }
		}

- 模式 #2：一次性安全发布（one-time safe publication）  
解决双重检查锁定（double-checked-locking）这类问题  

		public class BackgroundFloobleLoader {
		    public volatile Flooble theFlooble;
		
		    public void initInBackground() {
		        // do lots of stuff
		        theFlooble = new Flooble();  // this is the only write to theFlooble
		    }
		}
		
		public class SomeOtherClass {
		    public void doWork() {
		        while (true) { 
		            // do some stuff...
		            // use the Flooble, but only if it is ready
		            if (floobleLoader.theFlooble != null) 
		                doSomething(floobleLoader.theFlooble);
		        }
		    }
		}
如果 theFlooble 引用不是 volatile 类型，doWork() 中的代码在解除对 theFlooble 的引用时，将会得到一个不完全构造的 Flooble。

- 模式 #3：独立观察（independent observation）  
		没太理解 :(  

		public class UserManager {
		    public volatile String lastUser;
		
		    public boolean authenticate(String user, String password) {
		        boolean valid = passwordIsValid(user, password);
		        if (valid) {
		            User u = new User();
		            activeUsers.add(u);
		            lastUser = user;
		        }
		        return valid;
		    }
		}
- 模式 #4：“volatile bean” 模式
- 模式 #5：开销较低的读－写锁策略

## synchronized
可以解决原子性（atomicity）和 可见性（visibility）两个问题

[谨慎的使用synchronized(this)](http://stackoverflow.com/questions/309631/what-cases-require-synchronized-method-access-in-java#309677)：  

1. 此实例的所有方法都用同一把锁，降低性能  
2. 这个锁用户也可以访问不安全，容易引起死锁：  

		MyClass c = new MyClass();
		synchronized(c) {
		    ...
		}
这种用法会很少有人用么？不是，你要遍历synchronizedMap的时候，java文档明确要求你要对其锁定。
   
		//It is imperative that the user manually synchronize on the returned
		// map when iterating over any of its collection views:	
		 Map m = Collections.synchronizedMap(new HashMap());
		     ...
		 Set s = m.keySet();  // Needn't be in synchronized block
		     ...
		 synchronized (m) {  // Synchronizing on m, not s!
		     Iterator i = s.iterator(); // Must be in synchronized block
		     while (i.hasNext())
		         foo(i.next());
		 }

	推荐使用自己的私有变量做锁，可以参考SynchronizedMap源码，SynchronizedMap没有使用synchronized(this)，而是自己定义了final Object mutex;.或者如下：   

		public class MyClass {
		    private int instanceVar;
		    private final Object lock = new Object();     // must be final!	
		    public void setInstanceVar() {
		        synchronized(lock) {
		            instanceVar++;
		        }
		    }
		}

Object wait(), notify()  
<http://www.cnblogs.com/x_wukong/p/4009709.html>  
在JAVA中的Object类型中，都是带有一个内存锁的，在有线程获取该内存锁后，其它线程无法访问该内存，从而实现JAVA中简单的同步、互斥操作。如果只是简单的想要实现在JAVA中的线程互斥，使用synchronized。但如果需要在线程间相互唤醒的话就需要借助Object.wait(), Object.nofity()了。
Obj.wait(),Obj.notify必须在synchronized(Obj){...}语句块内。wait就是说线程在获取对象锁后，主动释放对象锁，同时本线程休眠。直到有其它线程调用对象的notify()唤醒该线程，才能继续获取对象锁，并继续执行。要注意的是notify()调用后，并不是马上就释放对象锁的，而是在相应的synchronized(){}语句块执行结束，自动释放锁后。Thread.sleep()与Object.wait()二者都可以暂停当前线程，释放CPU控制权，主要的区别在于Object.wait()在释放CPU同时，释放了对象锁的控制。
例子：建立三个线程，A线程打印10次A，B线程打印10次B,C线程打印10次C，要求线程同时运行，交替打印10次ABC。

## ReentrantLock
一个可重入的互斥锁定 Lock，它具有与使用 synchronized 方法和语句所访问的隐式监视器锁定相同的一些基本行为和语义，但功能更强大。reentrant锁意味着它有一个与锁相关的获取计数器，如果拥有锁的某个线程再次得到锁，那么获取计数器就加1，然后锁需要被释放两次才能获得真正释放。
正在等候获得锁的线程，也无法通过投票得到锁，如果不想等下去，也就没法得到锁
它拥有与 synchronized 相同的并发性和内存语义，但是添加了  

1. lock，<https://www.zhihu.com/question/36771163/answer/68974735>
	1. 如果该锁没有被另一个线程保持，则获取该锁并立即返回，将锁的保持计数设置为 1。
	2. 如果当前线程已经保持该锁，则将保持计数加 1，并且该方法立即返回。
	3. 	如果该锁被另一个线程保持，则出于线程调度的目的，禁用当前线程，并且在获得锁之前，该线程将一直处于休眠状态，此时锁保持计数被设置为 1。
2. tryLock(long timeout, TimeUnit unit),定时锁等候-在等候时间内未获取锁，线程会自己中断
2. lockInterruptibly可中断锁等候,线程等候可以自己中断，也可以由别的线程中断
	1. 如果当前线程未被中断，则获取锁。 
	2. 如果该锁没有被另一个线程保持，则获取该锁并立即返回，将锁的保持计数设置为 1。 
	3. 如果当前线程已经保持此锁，则将保持计数加 1，并且该方法立即返回。 
	4. 如果锁被另一个线程保持，则出于线程调度目的，禁用当前线程，并且在发生以下两种情况之一以前，该线程将一直处于休眠状态： 
		1. 锁由当前线程获得；或者 
		2. 其他某个线程中断当前线程。 
	3. 如果当前线程获得该锁，则将锁保持计数设置为 1。 如果当前线程： 
		1. 在进入此方法时已经设置了该线程的中断状态；或者 
		2. 在等待获取锁的同时被中断。 
   	则抛出 InterruptedException，并且清除当前线程的已中断状态。 

3. 锁投票 ??


ReentrantLock 将由最近成功获得锁定，并且还没有释放该锁定的线程所拥有。当锁定没有被另一个线程所拥有时，调用 lock 的线程将成功获取该锁定并返回。如果当前线程已经拥有该锁定，此方法将立即返回。可以使用 isHeldByCurrentThread() 和 getHoldCount() 方法来检查此情况是否发生。

- 此类的构造方法接受一个可选的公平参数。设置为 true时，在多个线程的争用下，这些锁定倾向于将访问权授予等待时间最长的线程。否则此锁定将无法保证任何特定访问顺序。与采用默认设置（使用不公平锁定）相比，使用公平锁定的程序在许多线程访问时表现为很低的总体吞吐量（即速度很慢，常常极其慢），但是在获得锁定和保证锁定分配的均衡性时差异较小。不过要注意的是，公平锁定不能保证线程调度的公平性。因此，使用公平锁定的众多线程中的一员可能获得多倍的成功机会，这种情况发生在其他活动线程没有被处理并且目前并未持有锁定时。还要注意的是，未定时的 tryLock 方法并没有使用公平设置。因为即使其他线程正在等待，只要该锁定是可用的，此方法就可以获得成功。

- 建议总是，**使用 try 块来调用 lock**。:synchronized是在JVM层面上实现的，如果在代码执行时出现异常，JVM会自动释放锁；lock是通过代码实现的，要保证锁一定会被释放，就必须将unlock放到finally块中

> 	public class MyThread extends Thread{
> 	    TestReentrantLock lock;
> 	    private int id;
> 	    public MyThread(int i, TestReentrantLock test){
> 	        this.id=i;
> 	        this.lock=test;
> 	    }
> 	    public void run(){
> 	        lock.print(id);
> 	    }
> 	    public static void main(String args[]){
> 	        ExecutorService service=Executors.newCachedThreadPool();
> 	        TestReentrantLock lock=new TestReentrantLock();
> 	        for(int i=0;i<10;i++){
> 	            service.submit(new MyThread(i,lock));
> 	        }
> 	        service.shutdown();
> 	    }
> 	}
> 	class TestReentrantLock{
> 	    private ReentrantLock lock=new ReentrantLock();
> 	    public void print(int str){
> 	        try{
> 	            lock.lock();
> 	            System.out.println(str+"获得");
> 	            Thread.sleep((int)(Math.random()*1000));
> 	        }
> 	        catch(Exception e){
> 	            e.printStackTrace();
> 	        }
> 	        finally{
> 	            System.out.println(str+"释放");
> 	            lock.unlock();
> 	        }
> 	    }
> 	}

**条件变量Condition**  
条件（也称为条件队列 或条件变量）为线程提供了一个含义，以便在某个状态条件现在可能为 true 的另一个线程通知它之前，一直挂起该线程（即让其“等待”）。Condition接口await*对应于Object.wait，signal对应于Object.notify，signalAll对应于Object.notifyAll。参考ArrayBlockingQueue的实现


## **[CountDownLatch(比赛计时)](http://tool.oschina.net/uploads/apidocs/jdk-zh/java/util/concurrent/CountDownLatch.html)** 
- 一个或多个线程一直等待，直到一组正在其他线程中执行的操作完成通知。
- 用给定的计数 初始化 CountDownLatch。调用countDown() 方法，在当前计数到达零之前，await 方法会一直受阻塞。
- 只能使用一次，计数无法被重置。如果需要重置计数，请考虑使用 CyclicBarrier。 
- CountDownLatch 是一个通用同步工具，它有很多用途。将计数 1 初始化的 CountDownLatch 用作一个简单的开/关锁存器，或入口：在通过调用 countDown() 的线程打开入口前，所有调用 await 的线程都一直在入口处等待。
- 用 N 初始化的 CountDownLatch 可以使一个线程在 N 个线程完成某项操作之前一直等待，或者使其在某项操作完成 N 次之前一直等待。
- CountDownLatch 的一个有用特性是，它不要求调用 countDown 方法的线程等到计数到达零时才继续，而在所有线程都能通过之前，它只是阻止任何线程继续通过一个 await
>     public class TestCountDownLatch {
>     public static void main(String[] s) throws InterruptedException {
>         final CountDownLatch begin = new CountDownLatch(1);   // 开始的倒数锁
>         final CountDownLatch end = new CountDownLatch(10); // 结束的倒数锁
>         final ExecutorService exec = Executors.newFixedThreadPool(10);// 十名选手
>         for (int index = 0; index < 10; index++) {
>             final int NO = index + 1;
>             Runnable run = new Runnable() {
>                 public void run() {
>                     try {
>                         begin.await();//一直阻塞
>                         Thread.sleep((long) (Math.random() * 10000));
>                         System.out.println("No." + NO + " arrived");
>                     } catch (InterruptedException e) {
>                     } finally {
>                         end.countDown();
>                     }
>                 }
>             };
>             exec.submit(run);
>         }
>         System.out.println("Game Start");
>         begin.countDown();
>         end.await();
>         System.out.println("Game Over");
>         exec.shutdown();
>     }
> 	}

## CyclicBarrier(三个旅行团)
它允许一组线程互相等待，直到到达某个公共屏障点 (common barrier point)。
在涉及一组固定大小的线程的程序中，这些线程必须不时地互相等待，此时 CyclicBarrier 很有用。因为该 barrier 在释放等待线程后可以重用，所以称它为循环 的 barrier。
CyclicBarrier 支持一个可选的 Runnable 命令，在一组线程中的最后一个线程到达之后（但在释放所有线程之前），该命令只在每个屏障点运行一次。若在继续所有参与线程之前更新共享状态，此屏障操作 很有用。
> 	public class TestCyclicBarrier {
> 	    private static int[] timeWalk = { 5, 8, 15, 15, 10 };// 徒步需要的时间: Shenzhen, Guangzhou, Shaoguan, Changsha, Wuhan
> 	    private static int[] timeSelf = { 1, 3, 4, 4, 5 };// 自驾游
> 	    private static int[] timeBus = { 2, 4, 6, 6, 7 };// 旅游大巴
> 	
> 	    static String now() {
> 	        SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");
> 	        return sdf.format(new Date()) + ": ";
> 	    }
> 	
> 	    static class Tour implements Runnable {
> 	        private int[] times;
> 	        private CyclicBarrier barrier;
> 	        private String tourName;
> 	        public Tour(CyclicBarrier barrier, String tourName, int[] times) {
> 	            this.times = times;
> 	            this.tourName = tourName;
> 	            this.barrier = barrier;
> 	        }
> 	        public void run() {
> 	            try {
> 	                Thread.sleep(times[0] * 1000);
> 	                System.out.println(now() + tourName + " Reached Shenzhen");
> 	                barrier.await();
> 	                Thread.sleep(times[1] * 1000);
> 	                System.out.println(now() + tourName + " Reached Guangzhou");
> 	                barrier.await();
> 	                Thread.sleep(times[2] * 1000);
> 	                System.out.println(now() + tourName + " Reached Shaoguan");
> 	                barrier.await();
> 	                Thread.sleep(times[3] * 1000);
> 	                System.out.println(now() + tourName + " Reached Changsha");
> 	                barrier.await();
> 	                Thread.sleep(times[4] * 1000);
> 	                System.out.println(now() + tourName + " Reached Wuhan");
> 	                barrier.await();
> 	            } catch (InterruptedException e) {
> 	            } catch (BrokenBarrierException e) {
> 	            }
> 	        }
> 	    }
> 	    public static void main(String[] s) {
> 	        // 三个旅行团
> 	        CyclicBarrier barrier = new CyclicBarrier(3);
> 	        ExecutorService exec = Executors.newFixedThreadPool(3);
> 	        exec.submit(new Tour(barrier, "WalkTour", timeWalk));
> 	        exec.submit(new Tour(barrier, "SelfTour", timeSelf));
> 	        //当我们把下面的这段代码注释后，会发现，程序阻塞了，无法继续运行下去。
> 	        exec.submit(new Tour(barrier, "BusTour", timeBus));
> 	        exec.shutdown();
> 	    }
> 	}

## Semaphore(排队上厕所)
一个计数信号量。从概念上讲，信号量维护了一个许可集合。如有必要，在许可可用前会阻塞每一个 acquire()，然后再获取该许可。每个 release() 添加一个许可，从而可能释放一个正在阻塞的获取者。但是，不使用实际的许可对象，Semaphore 只对可用许可的号码进行计数，并采取相应的行动。
Semaphore 通常用于限制可以访问某些资源（物理或逻辑的）的线程数目。
> 	public class MySemaphore extends Thread{
> 	    Semaphore position;
> 	    private int id;
> 	    public  MySemaphore(int i,Semaphore s){
> 	        this.id=i;
> 	        this.position=s;
> 	    }
> 	    public void run(){
> 	        try{
> 	            if(position.availablePermits()>0){
> 	                System.out.println("顾客["+this.id+"]进入厕所，有空位"+ Thread.currentThread().getName());
> 	            }
> 	            else{
> 	                System.out.println("顾客["+this.id+"]进入厕所，没空位，排队"+ Thread.currentThread().getName());
> 	            }
> 	            position.acquire();
> 	            System.out.println("顾客["+this.id+"]获得坑位");
> 	            Thread.sleep((int)(Math.random()*1000));
> 	            System.out.println("顾客["+this.id+"]使用完毕");
> 	            position.release();
> 	        }
> 	        catch(Exception e){
> 	            e.printStackTrace();
> 	        }
> 	    }
> 	    public static void main(String[] a){
> 	        ExecutorService list= Executors.newCachedThreadPool();
> 	        Semaphore position=new Semaphore(2);
> 	        for(int i=0;i<10;i++){
> 	            list.submit(new MySemaphore(i+1,position));
> 	        }
> 	        list.shutdown();
> 	        position.acquireUninterruptibly(2);
> 	        System.out.println("使用完毕，需要清扫了");
> 	        position.release(2);
> 	        System.out.println("退出");
> 	    }
> 	}


## BlockingQueue

- 支持两个附加操作的 Queue 
	- 检索元素时等待队列变为非空，
	- 以及存储元素时等待空间变得可用。
- BlockingQueue 不接受 null 元素。试图 add、put 或 offer 一个 null 元素时，某些实现会抛出 NullPointerException。null 被用作指示 poll 操作失败的警戒值。
- BlockingQueue 可以是限定容量的。它在任意给定时间都可以有一个 remainingCapacity，超出此容量，便无法无阻塞地 put 额外的元素。
 没有任何内部容量约束的 BlockingQueue 总是报告 Integer.MAX_VALUE 的剩余容量。
- BlockingQueue 实现主要用于生产者-使用者队列，但它另外还支持 Collection 接口。因此，举例来说，使用 remove(x) 从队列中移除任意一个元素是有可能的。 然而，这种操作通常不 会有效执行，只能有计划地偶尔使用，比如在取消排队信息时。
- BlockingQueue 实现是线程安全的。所有排队方法都可以使用内部锁定或其他形式的并发控制来自动达到它们的目的。
 然而，大量的 Collection 操作（addAll、containsAll、retainAll 和 removeAll）没有 必要自动执行，除非在实现中特别说明。
 因此，举例来说，在只添加了 c 中的一些元素后，addAll(c) 有可能失败（抛出一个异常）。
- BlockingQueue 实质上不支持使用任何一种“close”或“shutdown”操作来指示不再添加任何项。
 这种功能的需求和使用有依赖于实现的倾向。例如，一种常用的策略是：对于生产者，插入特殊的 end-of-stream 或 poison 对象，并根据使用者获取这些对象的时间来对它们进行解释。
- 如果在插入数据的时候，想删除缓存可以使用如下

        while (!myqueue.offer(e)) {
            // remove elements as long as offer() fails.
            Request r = myqueue.poll();            
        }

##　CompletionService
将生产新的异步任务与使用已完成任务的结果分离开来的服务。生产者 submit 执行的任务。使用者 take 已完成的任务，并按照完成这些任务的顺序处理它们的结果。例如，CompletionService 可以用来管理异步 IO ，执行读操作的任务作为程序或系统的一部分提交， 然后，当完成读操作时，会在程序的不同部分执行其他操作，执行操作的顺序可能与所请求的顺序不同。通常，CompletionService 依赖于一个单独的 Executor 来实际执行任务，在这种情况下，CompletionService 只管理一个内部完成队列。ExecutorCompletionService 类提供了此方法的一个实现。

	public class MyCompletionService implements Callable<String{
	    private int id;
	    public MyCompletionService(int i){
	        this.id=i;
	    }
	    public static void main(String[] a) throws Exception{
	        ExecutorService service = Executors.newCachedThreadPool();
	        CompletionService<String> completion=new ExecutorCompletionService<String>(service);
	        for(int i=0;i<10;i++){
	            completion.submit(new MyCompletionService(i));
	        }
	        for(int i=0;i<10;i++){
	            System.out.println(completion.take().get());
	        }
	        service.shutdown();
	    }
	    public String call() throws Exception {
	        Integer time=(int)(Math.random()*1000);
	        try{
	            System.out.println(this.id+" start");
	            Thread.sleep(time);
	            System.out.println(this.id+" end");
	        }
	        catch(Exception e){
	            e.printStackTrace();
	        }
	        return this.id+":"+time;
	    }
	}

## ScheduledExecutorService
一个 ExecutorService，可安排在给定的延迟后运行或定期执行的命令。
schedule 方法使用各种延迟创建任务，并返回一个可用于取消或检查执行的任务对象。scheduleAtFixedRate 和 scheduleWithFixedDelay 方法创建并执行某些在取消前一直定期运行的任务。
用 Executor.execute(java.lang.Runnable) 和 ExecutorService 的 submit 方法所提交的命令，通过所请求的 0 延迟进行安排。
schedule 方法中允许出现 0 和负数延迟（但不是周期），并将这些视为一种立即执行的请求。
所有的 schedule 方法都接受相对 延迟和周期作为参数，而不是绝对的时间或日期。将以 Date 所表示的绝对时间转换成要求的形式很容易。
例如，要安排在某个以后的日期运行，可以使用：schedule(task, date.getTime() - System.currentTimeMillis(), TimeUnit.MILLISECONDS)。
但是要注意，由于网络时间同步协议、时钟漂移或其他因素的存在，因此相对延迟的期满日期不必与启用任务的当前 Date 相符。
Executors 类为此包中所提供的 ScheduledExecutorService 实现提供了便捷的工厂方法。

	public class TestScheduledThread {
	    public static void main(String[] a) {
	        final ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(2);
	        final Runnable beeper = new Runnable() {
	            int count = 0;
	            public void run() {
	                System.out.println(new Date() + " beep " + (++count) + ", "+ Thread.currentThread().getName());
	            }
	        };
	        // 1秒钟后运行，并每隔2秒运行一次
	        final ScheduledFuture beeperHandle = scheduler.scheduleAtFixedRate(beeper, 1, 2, SECONDS);
	        // 2秒钟后运行，并每次在上次任务运行完后等待5秒后重新运行
	        final ScheduledFuture beeperHandle2 = scheduler.scheduleWithFixedDelay(beeper, 2, 5, SECONDS);
	        // 30秒后结束关闭任务，并且关闭Scheduler
	        scheduler.schedule(new Runnable() {
	            public void run() {
	                beeperHandle.cancel(true);
	                beeperHandle2.cancel(true);
	                scheduler.shutdown();
	            }
	        }, 30, SECONDS);
	    }
	}

## Future
新建一个线程执行耗时操作，同时返回一个Future对象给客户端，此时客户端得不到运行结束之后的结果，可以去做其他事情，最后再从future中拿结果  

Future主要接口  

	public interface Future<V> {
	    boolean cancel(boolean mayInterruptIfRunning);
	    boolean isCancelled();
	    boolean isDone();
	    V get() throws InterruptedException, ExecutionException;
	    V get(long timeout, TimeUnit unit)
	        throws InterruptedException, ExecutionException, TimeoutException;
	}

- cancel方法用来取消任务，如果取消任务成功则返回true，如果取消任务失败则返回false。参数mayInterruptIfRunning表示是否允许取消正在执行却没有执行完毕的任务，如果设置true，则表示可以取消正在执行过程中的任务。如果任务已经完成，则无论mayInterruptIfRunning为true还是false，此方法肯定返回false，即如果取消已经完成的任务会返回false；如果任务正在执行，若mayInterruptIfRunning设置为true，则返回true，若mayInterruptIfRunning设置为false，则返回false；如果任务还没有执行，则无论mayInterruptIfRunning为true还是false，肯定返回true。
- isCancelled方法表示任务是否被取消成功，如果在任务正常完成前被取消成功，则返回 true。
- isDone方法表示任务是否已经完成，若任务完成，则返回true；
- get()方法用来获取执行结果，这个方法会产生阻塞，会一直等到任务执行完毕才返回；
- get(long timeout, TimeUnit unit)用来获取执行结果，如果在指定时间内，还没获取到结果，就直接返回null。


使用示例

	public class CallableAndFuture {
	    public static void main(String[] args) {
	        Callable<Integer> callable = new Callable<Integer>() {
	            public Integer call() throws Exception {
	                return new Random().nextInt(100);
	            }
	        };
	        FutureTask<Integer> future = new FutureTask<Integer>(callable);
	        new Thread(future).start();
	        try {
	            Thread.sleep(5000);// 可能做一些事情
	            System.out.println(future.get());
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        } catch (ExecutionException e) {
	            e.printStackTrace();
	        }
	    }
	}


## ReentrantReadWriteLock
读写锁维护了一对相关的锁，一个用于只读操作，一个用于写入操作。只要没有writer，读取锁可以由多个reader线程同时保持。写入锁是独占的。