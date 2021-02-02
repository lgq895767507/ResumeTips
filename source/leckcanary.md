## 内存泄漏LeakCanary检测原理分析

#### 参考资料
* https://www.jianshu.com/p/46f98bddd636

#### 原理
* 内存泄露判断主要是用到了WeakReference和ReferenceQueue，弱引用对象回收了，弱引用对象就会在ReferenceQueue里，如果ReferenceQueue里没有，就说明可能泄露

```java

void ensureGone(final KeyedWeakReference reference, String referenceName, final long watchStartNanoTime) {
        long gcStartNanoTime = System.nanoTime();
        long watchDurationMs = NANOSECONDS.toMillis(gcStartNanoTime - watchStartNanoTime);

        removeWeaklyReachableReferences();

        if (gone(reference)) {
            return;
        }
        gcTrigger.runGc();
        removeWeaklyReachableReferences();
        if (!gone(reference)) {
            //do HeapDump, HeapAnalyzer
            Log.d("RefWatcher", Thread.currentThread().getName());
            Log.d("RefWatcher",referenceName + " leaked!");
        }
        return;
    }

```

* 可能泄露是因为GC不一定及时，所以LeakCanary会再调一次GC，然后再检测ReferenceQueue是否存在回收的对象。如果这次还没有就是泄露了，后面的逻辑就是给HAHA分析，Notification通知。