## [KOOM](https://github.com/KwaiAppTeam/KOOM/blob/v1.0.5/README.zh-CN.md)

1. 为什么要有koom?他解决了leakCanary的什么问题？
   * 采集内存镜像需要暂停虚拟机，以确保在内存数据拷贝到磁盘的过程中，引用关系不会发生变化，暂停时间通常长达10秒以上，对用户来讲是难以接受的，这也是LeakCanary官方不推荐线上使用的重要原因之一。利用Copy-on-write机制，fork子进程dump内存镜像，可以完美解决这一问题，fork成功以后，父进程立刻恢复虚拟机运行，子进程dump内存镜像期间不会受到父进程数据变动的影响。
   * leakCanary只能监控activity或者fragment下场景，无法监控到大对象、频繁分配等问题
   * koom功能全面的支持线上大规模部署的闭环监控系统
   * 比leakCanary更好的检测性能，fork子进程进行dump分析
   * leakCanary泄漏检测触发机制会主动GC,能会造成用户可感知的卡顿,而KOOM采用内存阈值监控来触发镜像采集。
   * [copy-on-write](https://zhuanlan.zhihu.com/p/136428913) 是计算机领域相当经典的优化思想，当然你如果问一个Java 开发者 copy-on-write 有什么作用？他们往往第一反应就是：优雅地解决读多写少场景下的并发问题。

2. koom的原理是什么？难点在哪里？