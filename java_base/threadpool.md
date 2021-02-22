#### 一）线程池

ExecutorService是Java提供的用于管理线程池的类。该类的两个作用：控制线程数量和重用线程

4种常用的线程池
1. Executors.newCachedThreadPool()：可缓存线程池

```java

 public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }

```

2. Executors.newFixedThreadPool(int n):创建一个可重用固定个数的线程池

```java

 public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }

```

3. Executors.newScheduledThreadPool(int n)：创建一个定长线程池，支持定时及周期性任务执行

```java
    public ScheduledThreadPoolExecutor(int corePoolSize) {
        super(corePoolSize, Integer.MAX_VALUE,
              DEFAULT_KEEPALIVE_MILLIS, MILLISECONDS,
              new DelayedWorkQueue());
    }

```

4. Executors.newSingleThreadExecutor(): 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。


```java

   public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }

```

#### 原理：
首先我们看下当一个新的任务提交到线程池之后，线程池是如何处理的 
1、线程池判断核心线程池里的线程是否都在执行任务。如果不是，则创建一个新的工作线程来执行任务。如果核心线程池里的线程都在执行任务，则执行第二步。 
2、线程池判断工作队列是否已经满。如果工作队列没有满，则将新提交的任务存储在这个工作队列里进行等待。如果工作队列满了，则执行第三步 
3、线程池判断线程池的线程是否都处于工作状态。如果没有，则创建一个新的工作线程来执行任务。如果已经满了，则交给饱和策略来处理这个任务

#### 二）线程池构造方法各个参数的含义？
* https://www.cnblogs.com/itnotes-4ever/p/12811812.html
* core
* max
* keeplive
* unit
* blockQueue
* factory
* RejectedExecutionHandler


#### 三）RejectedExecutionHandler四种策略
1. AbortPolicy: 该策略是直接将提交的任务抛弃掉，并抛出RejectedExecutionException异常。
2. DiscardPolicy:该策略也是将任务抛弃掉（对于提交的任务不管不问，什么也不做），不过并不抛出异常。
3. DiscardOldestPolicy:该策略是当执行器未关闭时，从任务队列workQueue中取出第一个任务，并抛弃这第一个任务，进而有空间存储刚刚提交的任务。使用该策略要特别小心，因为它会直接抛弃之前的任务。
4. CallerRunsPolicy:该策略并没有抛弃任何的任务，由于线程池中已经没有了多余的线程来分配该任务，该策略是在当前线程（调用者线程）中直接执行该任务。


#### 四）线程池核心线程数和工作线程数有什么区别？
* 如何配置最大线程数？
   1. CPU密集型任务： CPU 核数 +1
   2. IO密集型任务： 线程数 = CPU 核心数 / (1 - 阻塞系数) // 一般认为在 0.8 ~ 0.9 之间

#### 五）线程池为什么要使用阻塞队列而不使用非阻塞队列？
* 多线程环境支持，多个线程可以安全的访问队列
* 支持生产和消费等待，多个线程之间互相配合，当队列为空的时候，消费线程会阻塞，等待队列不为空；当队列满了的时候，生产线程就会阻塞，直到队列不满。


#### 六）线程池中的任务可以实现按照优先级执行么，如何实现？
* 替换线程池默认的阻塞队列为 PriorityBlockingQueue

#### 七）线程池的设计用到了那种设计思想
* 生产者消费者模型