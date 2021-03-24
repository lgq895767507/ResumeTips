#### glide原理简述

 * 资源封装key value,使用一次count++,不使用count--
 * 活动缓冲weakReference弱引用，ReferenceQueue<Value>
 * 内存缓存继承自lruchche,lrucache是最少使用会被移除的算法，利用LinkHashmap<key,value>的特性:双向链表的hashMap
 * 磁盘缓存
 * 生命周期
 * 加载图片
 * 如果是token问题，通过重写getCacheKey(),去除token相关的数据作为key即可

```java

 /**
     * The head (eldest) of the doubly linked list.
     */
    transient LinkedHashMapEntry<K,V> head;

    /**
     * The tail (youngest) of the doubly linked list.
     */
    transient LinkedHashMapEntry<K,V> tail;

```

* fragment

#### lruCache

```java

class LRUCache extends LinkedHashMap<Integer, Integer>{
    private int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75F, true);
        this.capacity = capacity;
    }

    public int get(int key) {
        return super.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
        super.put(key, value);
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity; 
    }
}

```

#### glide源码分析参考
* https://juejin.cn/post/6900548494818279432