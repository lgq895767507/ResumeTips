#### 常见的基础面试题

1. volatile关键字
   * 保证了可见性和禁止指令重排序
   * 可见性：添加volatile关键字后，汇编会多执行一句"lock addl $0x0, (%esp)"操作，这个操作相当于内存屏障。指令重排序时不能把后面的指令重排序到内存屏障之前的位置，而这个lock操作使得本CPU的Cache写入内存，该写入动作会引起别的CPU或者别的内核无效化。相当于对Cache中的变量做了一次"store"和"write"操作，所以通过这样一个空操作，可以对其他CPU可见。
   * 指令重排序：指CPU允许将多条指令不按程序规定的顺序分发给各相应电路单元处理。

2. 序列化和反序列化是怎么实现的
3. string stringbuild stringbuffer的区别？stringbuild的的数据结构是怎样的？
4. kotlin view的使用在哪个生命周期访问view会为空
5. http请求的数据格式是怎样的
6. kotlin的in和out
7. mvvm,mvp,mvvm的区别？
8. 属性动画和帧动画的区别
9. 属性动画的原理
10. ‌页面滑动卡顿了，你有什么方式或者工具来处理？
11. ‌代码混淆后，你是如何分析crash代码的？
12. ‌为什么要用泛型？泛型中消除泛型是什么意思？
13. ‌hashcode和equals什么情况下需要重写？
14. 开启硬件加速和不开启硬件加速view的绘制有什么区别？
15. linkhashmap是如何实现查询和插入有序的
16. 为什么要用组件化
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
21. IdleHandler
    * MessageQueue中一个回调接口
    * 当messageQueue消息执行结束后，线程空闲了，就会回调此监听