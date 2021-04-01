 ## android性能优化

 #### 性能优化会关注哪些指标?
 * 流畅
 * 稳定
 * 资源节省
 
 * app启动优化：黑白屏解决方案--修改自定义继承apptheme,设置背景为启动页的背景，可以设置为layout-list
   * 代码优化：如何检测启动时间的耗时呢？
     1. Debug.startMethodTracing("filePath") //application onCreate()启动的时候调用，结束的时候调用stopMethodTracing()
     2. 异步线程优化方案, [优秀文章:有向无环图](https://mp.weixin.qq.com/s/LU5G5GT4rTtr2Rsnx4SOQQ)
     3. 懒加载优化方案
     4. [参考alibaba异步task方案alpha](https://github.com/alibaba/alpha)
  * ui优化
     * 目的：要小于16ms/帧刷新率，显示才不会卡顿。
        * 减少cpu解析xml的时间
           1.  减少布局层级 
           2. 使用Merge减少多余一层ViewGropu容器
           3. 使用ViewStub进行布局的懒加载
        * 减少gpu重复绘制
           1. 开发者选项打开gpu过度绘制开关 
           2. 前景背景是否重复绘制
           3. 自定义view是否进行裁剪
      
  * 内存优化
    * java虚拟机结构:程序计数器，虚拟机栈，本地方法栈，堆、方法区
    * java内存四种引用类型:强引用，软引用，弱引用，虚引用
    * GC算法和回收机制
      * 引用计数算法: Count++
      * 可达性分析法：GCRoot
    * GCRoot对象有哪些？
      *  java虚拟机栈(栈帧中的本地变量表)中的引用的对象。
      *  方法区中的类静态属性引用的对象。 
      *  方法区中的常量引用的对象。 
      *  本地方法栈中JNI本地方法的引用对象。
   * 垃圾回收常见的算法
         * 标记清除
         * 复制
         * 标记整理
         * 分代收集
      * 垃圾收集器
         * [Edem : from : to = 8 :1 : 1 新生代三个区的比例](https://bbs.huaweicloud.com/blogs/126777) 
         * Serial收集器：单线程操作，暂停其他任务(STW "Stop the world"):新生代用的复制算法，老年代用的标记整理算法
         * ParNew收集器：多线程版本的Serial，仍然也会STW
         * Parallel Scavenge收集器：通过控制吞吐量来控制停顿的时间，也被成为吞吐量优先收集器
         * CMS收集器：获取最短回收停顿时间为目标的收集器，并发收集，低停顿。 标记清除算法
         * G1收集器：当今收集器技术发展最前沿成果之一。并行与并发，分代收集，空间整合，可预测停顿。 
      * 从AS的profile工具分析内存情况，下载hprof文件，格式转换为mat可分析的格式，

#### 性能分析
1. 布局优化
  * 减少层级
  * <include>,<Merge>,<ViewStub> 按需要加载标签
      * 其实ViewStub就是一个宽高都为0的一个View，它默认是不可见的，只有通过调用setVisibility函数或者Inflate函数才会将其要装载的目标布局给加载出来，从而达到延迟加载的效果，这个要被加载的布局通过android:layout属性来设置。
      * Merge就是为了解决重复定义相同布局的问题。
       
2. 绘制优化
  * onDraw()避免执行大量的操作
    1. 避免创建新的局部对象
    2. 不要做耗时任务

3. 内存泄漏优化
  * 避免写内存泄漏的代码
    1. 静态变量导致内存泄漏。 static context = this
    2. 单例模式引起的内存泄漏。单例模式的生命周期和Application是一致的，当单例注册了某个activity的时候，一定要在actiivty销毁的时候解注册
    3. 属性动画导致内存泄漏。无限循环的动画会在activity销毁后仍然继续播放下去，则会一直持有当前的activity对象，因此在销毁的时候要animal.cancel()停止动画
    
  * 通过MAT工具查找一些不易被发现的内存泄漏地方
    1. hprof文件


4. 响应速度优化
  * 避免在主线程做耗时操作
  * 发生anr的条件是：当activity主线程在5s内没有响应点击或者输入操作，则会发生anr,anr的的信息会存储到/data/anr/trace.txt中。

5. listView和bitmap优化
  * listview--viewholder避免在getview耗时操作
  * 快速滑动不太合适开启大量的线程异步任务
  * 开启硬件加速是更流畅

  * bitmap:bitmapFactory.Options来对图片进行采样
  * 主要用到inSampleSize参数

6. 线程优化
  * 采用线程池，避免创建存在大量的thread,从而避免创建和销毁带来的开销
  * 同时还能有效的控制线程池的最大并发数，避免相互抢占资源导致堵塞
  * 节约线程切换的开销
  
7. 性能优化建议
  * 常量使用static final来修饰
  * 使用sparrayArray数据结构，android 特有的数据结构优于其他
  * 适当的使用软引用和弱引用
  * 采用内存缓存和磁盘缓存
  * 尽量采用静态内部类，避免由于内部类导致的内存泄漏