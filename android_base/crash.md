#### 异常分析
* 实现UncaughtExceptionHandler
* [anr分析](https://www.jianshu.com/p/a1a27619b0ef)
   * ANR异常捕获是通过FileObserver实现的，通过监听有写入/data/anr/trace.txt时，使用ActivityManager.getProcessesInErrorState()获取当前进程的所有异常信息List.过滤掉其他非ANR异常。


#### bugly原理分析
* 