## ANR定位与分析
* [anr分析](https://www.jianshu.com/p/a1a27619b0ef)
* ANR异常捕获是通过FileObserver实现的，通过监听有写入/data/anr/trace.txt时，使用ActivityManager.getProcessesInErrorState()获取当前进程的所有异常信息List.过滤掉其他非ANR异常。


#### ANR治理
1. 问题一：如礼物资源、勋章资源、大航海资源每个直播间都会重复用到，目前的策略是退出直播间情况内存缓存，然后下次进入再从磁盘读取耗费时间概率触发ANR，同时频繁触发GC造成用户卡顿；
2. 问题二：不同模块设计了自己的内存缓存方案，如礼物Lru最大总内存1/8，勋章Lru最大总内存1/8，大航海Lru最大总内存1/8，单独看每一个模块都没有问题，但是在直播间整体看会发现所有加起来基本都要分配满了；

##### 解决方案：
1. 统一内存管理，一个LruCache管理所有的模块的内存缓存；
2. 监控直播间的生命周期，短时间切换直播间（上下滑动、内部跳转、回首页浏览跳转等）不会清空，长时间不进直播间才会清空； // 设置延时任务


#### 卡顿堆栈监控

##### 实现方案
1. 目前代码Looper printer只支持一个实例，已经被APM占用，所以需要APM同学提供扩展，将Looper printer事件抛出来，供业务方使用；
2. 业务方Looper printer事件耗时，如果超过阈值（kv控制超时时间，默认为4s），dump堆栈，走业务异常上报；

#### 礼物资源加载
1. 资源读取接入资源统一管理框架 ，提供异步读取能力；


#### shareperference为什么会卡顿
* commit直接在当前线程执行mcr.writtenToDiskLatch.await(),会阻塞当前线程，直到SharedPreferencesImpl.this.enqueueDiskWrite(
                mcr, null /* sync write on this thread okay */);写入结束。

```java

 @Override
        public boolean commit() {
            long startTime = 0;

            if (DEBUG) {
                startTime = System.currentTimeMillis();
            }

            MemoryCommitResult mcr = commitToMemory();

            SharedPreferencesImpl.this.enqueueDiskWrite(
                mcr, null /* sync write on this thread okay */);
            try {
                mcr.writtenToDiskLatch.await();
            } catch (InterruptedException e) {
                return false;
            } finally {
                if (DEBUG) {
                    Log.d(TAG, mFile.getName() + ":" + mcr.memoryStateGeneration
                            + " committed after " + (System.currentTimeMillis() - startTime)
                            + " ms");
                }
            }
            notifyListeners(mcr);
            return mcr.writeToDiskResult;
        }

```

* apply卡顿的原因：QueuedWork.addFinisher(awaitCommit);会把mcr.writtenToDiskLatch.await();任务提交到LinkedList<Runnable> sFinishers中，sFinishers的取值在waitToFinish()，并且会等待执行结束。而activityThread的handleStopActivity(),handlePauseActivity()都有 QueuedWork.waitToFinish();方法，也就是如果有未提交结束的runable(),那么就会一直等待执行。造成阻塞。

```java

@Override
        public void apply() {
            final long startTime = System.currentTimeMillis();

            final MemoryCommitResult mcr = commitToMemory();
            final Runnable awaitCommit = new Runnable() {
                    @Override
                    public void run() {
                        try {
                            mcr.writtenToDiskLatch.await();
                        } catch (InterruptedException ignored) {
                        }

                        if (DEBUG && mcr.wasWritten) {
                            Log.d(TAG, mFile.getName() + ":" + mcr.memoryStateGeneration
                                    + " applied after " + (System.currentTimeMillis() - startTime)
                                    + " ms");
                        }
                    }
                };

            QueuedWork.addFinisher(awaitCommit);

            Runnable postWriteRunnable = new Runnable() {
                    @Override
                    public void run() {
                        awaitCommit.run();
                        QueuedWork.removeFinisher(awaitCommit);
                    }
                };

            SharedPreferencesImpl.this.enqueueDiskWrite(mcr, postWriteRunnable);

            // Okay to notify the listeners before it's hit disk
            // because the listeners should always get the same
            // SharedPreferences instance back, which has the
            // changes reflected in memory.
            notifyListeners(mcr);
        }

```

##### 总结
1. commit 方式会阻塞调用的线程
2. apply 放法不会阻塞调用的线程，但是如果写入任务比较耗时，会阻塞住主线程，因为主线程有调用的代码，需要等写入任务执行完了才会继续往下执行









