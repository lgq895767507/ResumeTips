#### map

* Hashmap, HashTable, ConcurrentHashMap
* [ConcurrentHashMap](https://blog.csdn.net/weixin_44460333/article/details/86770169)

##### HashMap

* 默认大小16
* 负载因子0.75
* 底层基于 数组 + 链表组成
* 允许key和value都为空
* 1.8版本会根据冲突数量大小判断，大于等于7的时候转化为红黑树
* 非线程安全，会有并发问题
* 1.7版本链表是头插法，1.8版本是尾插法，采用头插法时会容易出现逆序且环形链表死循环问题

##### ConcurrentHashMap

* 1.7版本：Segment[] + HashEntry(数组+链表): https://mp.weixin.qq.com/s/1yWSfdz0j-PprGkDgOomhQ
* HashEntry的value和链表next都被volatile修饰
* 分段锁技术(Segment继承ReentrantLock)
* 1.8版本：CAS + synchronized
* 总的来说1.8版本的优化是对1.7版本链表查找慢的优化，红黑树的引入使得查找复杂度由O(n)降到O(log N)
