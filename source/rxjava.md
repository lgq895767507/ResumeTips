#### rxjava相关操作符

* 过滤操作符
  * 合并操作符
    * startwith
    * contractwith
    * zip
    * merge
  * 异常操作符
    * onErrorReturn
    * onErrorResumeNext
    * onExceptionResumeNext -- 可以处理是否兼容异常
    * retry -- false:不重试，true：一直重试
  * 线程切换
    * Schedulers.io()
    * Schedulers.newThread()
    * Schedulers.computation()
    * 上游分配切换线程会忽略后面配置的，下游分配切换线程会每次都去切换。
    * rxjava加载图片：异步加载，map变换为bitmap,线程切换为主线程。
  * 背压
    * Flowable--Subscribe
    * Observable--Observer
    * 背压策略
      * backpressureStrategy.error: 最大值128，超过报错，不会收到数据（同步会报错，异步不会报错，异步可以通过request来请求数据）
      * backpressureStrategy.BUFFER：缓存，如果超过等待

* 长问到的map和flatmap的区别
  * map: 只能一对一的转换
  * flatmap:既可以一对一的转换，还可以一对多的转换 