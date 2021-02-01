#### 常见的基础面试题

1. volatile关键字
   * 保证了可见性和禁止指令重排序
   * 可见性：添加volatile关键字后，汇编会多执行一句"lock addl $0x0, (%esp)"操作，这个操作相当于内存屏障。指令重排序时不能把后面的指令重排序到内存屏障之前的位置，而这个lock操作使得本CPU的Cache写入内存，该写入动作会引起别的CPU或者别的内核无效化。相当于对Cache中的变量做了一次"store"和"write"操作，所以通过这样一个空操作，可以对其他CPU可见。
   * 指令重排序：指CPU允许将多条指令不按程序规定的顺序分发给各相应电路单元处理。

2. 序列化和反序列化是怎么实现的
   * 序列化：把对象转换为字节序列的过程称为对象的序列化。
   * 反序列化：把字节序列恢复为对象的过程称为对象的反序列化。
   * Serializable
     * 序列化：ObjectOutputStream.writeObject
     * 反序列化：ObjectInputStream.readObject
   * Parcelable
     * 序列化：writeToParcel()
     * 反序列化：createFromParcel()

   * Parcelable和Serializable的区别：
     * 1）在使用内存的时候，Parcelable比Serializable性能高，所以推荐使用Parcelable。
     * 2）Serializable在序列化的时候会产生大量的临时变量，从而引起频繁的GC。
     * 3）Parcelable不能使用在要将数据存储在磁盘上的情况，因为Parcelable不能很好的保证数据的持续性在外界有变化的情况下。尽管Serializable效率低点，但此时还是建议使用Serializable 。

3. string stringbuild stringbuffer的区别？stringbuild的的数据结构是怎样的？
   * string: final类型
   * stringbuild:非线程安全
   * stringbuffer：线程安全，在方法上添加有互斥锁

4. kotlin view的使用在哪个生命周期访问view会为空
  

5. http请求的数据格式是怎样的
   * 请求行 + 请求头 + 数据体
   * 请求行： method + request-URI + http-version
   * 响应数据格式：状态行 + 响应头 + 响应正文
   * 状态行：HTTP-Version + Status-Code + Reason-Phrase //eg: HTTP/1.1 200 ok

6. kotlin的in和out
   * in是消费者，out是生产者。生产者表示可以获得类型数据，但是不能添加和修改，消费者表示可以添加和修改数据，但是不能获得具体的数据类型。
   * in对应java里面的super
   * out对象java里面的extends

7. mvc,mvp,mvvm的区别？
   * model, view, control. 相互关联，耦合
   * model, view, present. model和view通过present解耦，不直接关联.
   * model. view, viewmodel. viewmodel也就是视图的模型.

8. 属性动画和帧动画的区别
9.  属性动画的原理
10. ‌页面滑动卡顿了，你有什么方式或者工具来处理？


11. ‌代码混淆后，你是如何分析crash代码的？
    * 通过mapping.txt文件和混淆日志
    * 参考 https://blog.csdn.net/geekqian/article/details/78961835

12. ‌为什么要用泛型？泛型中消除泛型是什么意思？
    1. 类型安全
    2. 消除强制转换
    3. 潜在的性能收益
    *  在编译阶段会将所有的泛型替换为实际的具体类型，这就是泛型擦除。

13. ‌hashcode和equals什么情况下需要重写？
    * equals默认比较的是两个对象是否相等。String类重写了equals
    * 如果不重写hashCode的话，可能导致HashSet、HashMap不能正常的运作
    * 因为hashcode有这样的规定，相同的对象，必须具有相等的hascode，因此重写了equals，都需要重写hascode。

14. 开启硬件加速和不开启硬件加速view的绘制有什么区别？
    * 硬件加速开启时，CPU的工作是把绘制工作转换为GPU的操作

15. linkhashmap是如何实现查询和插入有序的
    * 双向链表数据结构
    * LinkedHashMapEntry<K,V> head,tail

16. 为什么要用组件化
    1. 整编效率太慢
    2. 便于测试
    3. 有几率复用
    4. 业务模块解耦

17. 什么情况下会导致内存泄露
    * 长生命周期的对象持有短生命周期的对象，短周期对象就无法及时释放
    * 广播unregisterrecevier
    * 数据链接没有关闭，io，socket，cursor，database
    * Context引用
    * handler

18. 如何避免OOM
    * 减少内存对象的占用
    * ArrayMap/SparseArray代替hashmap
    * 避免在android里面使用Enum
    * 减少bitmap的内存占用
    * StringBuilder 代替+
    * 避免重复创建局部变量
    * 避免无限递归

19. ANR
    * 在规定的时间内，没有响应
    * 按键或触摸事件在5s内无响应
    * BroadcastReceiver在10s时间内无法处理完成
    * Service在20s时间内无法处理完成
    * /data/anr/trace.xml文件分析

20. http/https
    * http协议的缺点
    * 通信使用明文，内容可能被窃听(重要密码泄露)
    * 不验证通信方身份，有可能遭遇伪装(跨站点请求伪造)
    * 无法证明报文的完整性，有可能已遭篡改(运营商劫持)
  * http + ssl/tls = https 
  * SSL/TLS协议的基本过程: 
    * 1） 客户端向服务器端索要并验证公钥
    * 2） 双方协商生成"对话密钥"
    * 3） 双方采用"对话密钥"进行加密通信。

21. IdleHandler
    * MessageQueue中一个回调接口
    * 当messageQueue消息执行结束后，线程空闲了，就会回调此监听

22. 线上日志如何回捞？
