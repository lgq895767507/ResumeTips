#### 线程安全的实现方法

* 互斥同步
  * synchronization 
  * ReentrantLock(重入锁)
* 非阻塞同步
  * CAS(compare-and-swap) --原子操作类：AtomicInteger
* 无同步方案
  * 线程本地存储(Thread Local Storage)
  * ThreadLocal类中，每个线程的Thread对象都有一个ThreadLocalMap对象，这个对象存储了一组以ThreadLocal.threadLocalHashCode为键，以本地线程变量为值的k-v值对，也即是每一个ThreadLocal对象都包含一个threadLocalHashCode值，通过这个就能找到本地线程变量。

#### 线程
##### 线程的5种状态(p384)
* 新建(New):创建后尚未启动
* 运行(Runnable)：操作系统线程状态中的Running和Ready,有可能正在执行，也有可能在等待CPU为它分配执行时间
* 无限期等待(Waiting)：不会被分配CPU执行时间，他们需要等待其他线程显示地唤醒
* 限期等待(Timed Waiting)：不会被分配CPU执行时间，一定时间后由系统自动唤醒
* 阻塞(Blocked):等待获取到一个排他锁，这个事件将在另外一个线程放弃这个锁的时候发生
* 结束(Terminated):已终止线程的线程状态，线程已经结束执行。