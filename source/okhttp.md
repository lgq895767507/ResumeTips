#### [okhttp源码流程分析](https://zhuanlan.zhihu.com/p/150708364)

网络的七层模型：应用层，表示层，会话层，传输层，网络层，数据链路层，物理层。
应用层：http,https,ftp...
传输层： socket

http1.0和http1.1版本的区别：
1.1版本新增了许多新的特性，尤其是connection:keep-alive作用特别大

http请求的格式：
* 请求头
  * 请求头的请求行:GET / HTTP/1.1/r/n
  * 请求头的请求集:host:www.baidu.com //一些key-value的请求集
* post请求
  * 请求头 POST / HTTP/1.1/r/n
  * host域名: host:www.baidu.com
  * 请求体长度 content-length
  * 请求体类型
    * form表单提交

 * 线程池使用了缓存方案：即线程池的核心线程数为0，工作线程数为Integer.max_value。

```java

说说okhttp是如何实现异步请求的？
答：首先通过创建OkHttpClient对象，调用okHttpClient.newCall()会返回一个Call对象,
再通过call.enqueue()异步请求，结果会回调给Callback接口。当调用call.enqueue()方法的时候，
实际上call对象是RealCall.newRealCall(this, request, forWebSocket = false)，RealCall是Call的子类，
RealCall的enqueue()方法中，首先会检查是否被executed，如果没有被执行过，接下来会执行  client.dispatcher.enqueue(AsyncCall(responseCallback))，
调用到分发器Dispatcher的enqueue()方法，传递的是AsyncCall(),AsyncCall是一个Runnable,Dispatcher的enqueue方法，readyAsyncCalls加入到待执行队列中，
接下来判断runningAsyncCalls是否满足条件小于最答的请求数64，并且小于同一个Host请求数5,则runningAsyncCalls队列会把任务asyncCall添加进去，并且executableCalls会添加到集合中，
然后执行asyncCall.executeOn(executorService)，executorService是一个缓存线程池，接下来通过执行executorService.execute(this)，这个this就是runnable,因此会执行run()方法中的getResponseWithInterceptorChain(),
则会经历一串责任链拦截器，通过层层回调返回response,并通过responseCallback接口回调给Callback最初调用的地方，到这里就执行完毕，然后就会执行一些清理工作。

```

####  拦截器
* 责任链模式
* 自定义拦截器interceptors
* retryAndFollowUpInterceptor 重试及重定向拦截器：负责请求的重试和重定向。
* BridgeInterceptor 桥接拦截器：给请求添加对应的 header 信息，处理响应结果的 header 信息。
* CacheInterceptor 缓存拦截器：根据当前获取的状态选择 网络请求 、读取缓存、更新缓存。
* ConnectInterceptor 连接拦截器：建立 http 连接。
* interceptors.addAll(networkIntclient.networkInterceptors())  // --- !forWebSocket
* CallServerInterceptor 

#### Application interceptors
* Don't need to worry about intermediate responses like redirects and retries.
* Are always invoked once, even if the HTTP response is served from the cache.
* Observe the application's original intent. Unconcerned with OkHttp-injected headers like If-None-Match.
* Permitted to short-circuit and not call Chain.proceed().
* Permitted to retry and make multiple calls to Chain.proceed().
  
#### Network Interceptors
* Able to operate on intermediate responses like redirects and retries.
* Not invoked for cached responses that short-circuit the network.
* Observe the data just as it will be transmitted over the network.
* Access to the Connection that carries the request.

> 简单的说，Application interceptors是自定义的拦截器，会在重定向拦截器之前，而且是原始的originRequest，至少会被调用一次，因此可以动态添加一些参数。
> Network Interceptors是在CacheInterceptor之后的，请求的参数是经过处理后，因此可以作为拦截网络日志HttpLoggingInterceptor。
