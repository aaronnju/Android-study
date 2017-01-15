# java.util.concurrent
www
## synchronized

## ReentrantLock
一个可重入的互斥锁定 Lock，它具有与使用 synchronized 方法和语句所访问的隐式监视器锁定相同的一些基本行为和语义，但功能更强大。
ReentrantLock 将由最近成功获得锁定，并且还没有释放该锁定的线程所拥有。当锁定没有被另一个线程所拥有时，调用 lock 的线程将成功获取该锁定并返回。如果当前线程已经拥有该锁定，此方法将立即返回。可以使用 isHeldByCurrentThread() 和 getHoldCount() 方法来检查此情况是否发生。

- 此类的构造方法接受一个可选的公平参数。设置为 true时，在多个线程的争用下，这些锁定倾向于将访问权授予等待时间最长的线程。否则此锁定将无法保证任何特定访问顺序。与采用默认设置（使用不公平锁定）相比，使用公平锁定的程序在许多线程访问时表现为很低的总体吞吐量（即速度很慢，常常极其慢），但是在获得锁定和保证锁定分配的均衡性时差异较小。不过要注意的是，公平锁定不能保证线程调度的公平性。因此，使用公平锁定的众多线程中的一员可能获得多倍的成功机会，这种情况发生在其他活动线程没有被处理并且目前并未持有锁定时。还要注意的是，未定时的 tryLock 方法并没有使用公平设置。因为即使其他线程正在等待，只要该锁定是可用的，此方法就可以获得成功。
- 建议总是，使用 try 块来调用 lock

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
	        CompletionService<Stringcompletion=new ExecutorCompletionService<String>(service);
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