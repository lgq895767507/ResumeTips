
#### 线程池

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

#### 线程池构造方法各个参数的含义？


#### 线程池核心线程数和工作线程数有什么区别？