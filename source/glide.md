#### glide原理简述

 * 资源封装key value,使用一次count++,不使用count--
 * 活动缓冲weakReference弱引用，ReferenceQueue<Value>
 * 内存缓存继承自lruchche,lrucache是最少使用会被移除的算法，利用LinkHashmap<key,value>的特性:双向链表的hashMap
 * 磁盘缓存
 * 生命周期
 * 加载图片
 * 如果是token问题，通过重写getCacheKey(),去除token相关的数据作为key即可


* fragment

#### glide源码分析参考
* https://juejin.cn/post/6900548494818279432