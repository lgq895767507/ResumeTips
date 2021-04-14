#### 异常分析
* 实现UncaughtExceptionHandler
* [anr分析](https://www.jianshu.com/p/a1a27619b0ef)
   * ANR异常捕获是通过FileObserver实现的，通过监听有写入/data/anr/trace.txt时，使用ActivityManager.getProcessesInErrorState()获取当前进程的所有异常信息List.过滤掉其他非ANR异常。
   * [anrwatchdog,blockcanary等比较](https://blog.csdn.net/sinat_23027487/article/details/89600158)
   * 自定义Printer,通过setMessageLogging(@Nullable Printer printer)，监听消息处理时间

#### bugly原理分析
1. WindowCanary
   * 很多业务比如大乱斗、天选时刻等会主动弹出窗口提示用户，但是不会移除上一次的弹窗。如果用户不操作界面，那么弹窗就会越来越多，直到触发异常

##### 实现方案
1. 实现一套弹窗监控方案（WindowCanary），用于监控弹窗个数，判断是否需要主动释放弹窗。
2. 通过registerFragmentLifecycleCallbacks获取DialogFragment的生命周期，如果为onCreate则弱引用队列持有，onDestroy则移除引用。
如果队列达到阈值（kV配置，默认19个），就触发队列内部最老的DialogFragment的dismiss方法，移除该弹窗。
