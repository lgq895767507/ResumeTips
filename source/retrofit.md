#### retrofit源码流程分析

#####  从使用的角度来分析源码

```java

 private fun request() {
        val retrofit = Retrofit.Builder().build();
        // 为了简单说明，就没有封装retrofit单例对象
        retrofit.create(INetWork::class.java)
                .getData()
    }

    interface INetWork {
        
        @GET("getDataUrl")
        fun getData(): Call<String>
        
    }


```
1. retrofit创建对象，调用create(final Class<T> service)，传入的service必须是接口类型。即：传入我们定义的接口类
2. 调用接口类中请求的方法，为什么可以直接调用，这是因为create()方法通过动态代理生成了接口T的实例对象，调用方法后，相当于执行了动态代理中的invoke方法，接下来看下create()的源码

```java

// 动态代理返回T接口实例对象
public <T> T create(final Class<T> service) {
    Utils.validateServiceInterface(service); // 检测是否是接口类型
    if (validateEagerly) {
      eagerlyValidateMethods(service);
    }
    return (T) Proxy.newProxyInstance(service.getClassLoader(), new Class<?>[] { service },
        new InvocationHandler() {
          private final Platform platform = Platform.get();

          @Override public Object invoke(Object proxy, Method method, Object... args) // 执行请求方法
              throws Throwable {
            // If the method is a method from Object then defer to normal invocation.
            if (method.getDeclaringClass() == Object.class) {
              return method.invoke(this, args);
            }
            if (platform.isDefaultMethod(method)) {
              return platform.invokeDefaultMethod(method, service, proxy, args);
            }
            ServiceMethod serviceMethod = loadServiceMethod(method);  // 解析方法中的参数，get,post等数据，返回ServiceMethod对象
            OkHttpCall okHttpCall = new OkHttpCall<>(serviceMethod, args);
            return serviceMethod.callAdapter.adapt(okHttpCall); // 通过调用adapt，代理执行call.enqueue()方法，call是一个接口，他的实例对象是RealCall，接着就走到了RealCall的enqueue()方法
          }
        });
  }


```
3. loadServiceMethod方法是对method的参数解析，来看下源码的解释

```java
  // 返回的是一个ServiceMethod对象
  ServiceMethod loadServiceMethod(Method method) {
    ServiceMethod result;
    synchronized (serviceMethodCache) {
      result = serviceMethodCache.get(method);
      if (result == null) {
        result = new ServiceMethod.Builder(this, method).build();  // 主要在于这句,解析注解参数，构建在build中
        serviceMethodCache.put(method, result);
      }
    }
    return result;
  }


  public ServiceMethod build() {
      callAdapter = createCallAdapter();
      responseType = callAdapter.responseType();
      if (responseType == Response.class || responseType == okhttp3.Response.class) {
        throw methodError("'"
            + Utils.getRawType(responseType).getName()
            + "' is not a valid response body type. Did you mean ResponseBody?");
      }
      responseConverter = createResponseConverter();

      for (Annotation annotation : methodAnnotations) {
        parseMethodAnnotation(annotation); // 解析注解类型参数，构造参数对象
      }

      ...

      return new ServiceMethod<>(this);
    }

```



4. 接着就和okhttp的流程类似了

```java

 // RealCall.kt
 override fun enqueue(responseCallback: Callback) {
    check(executed.compareAndSet(false, true)) { "Already Executed" }

    callStart()
    client.dispatcher.enqueue(AsyncCall(responseCallback))
  }


  // dispatcher.kt

   internal fun enqueue(call: AsyncCall) {
    synchronized(this) {
      readyAsyncCalls.add(call)  // 添加到准备队列中

      // Mutate the AsyncCall so that it shares the AtomicInteger of an existing running call to
      // the same host.
      if (!call.call.forWebSocket) {
        val existingCall = findExistingCallWithHost(call.host)
        if (existingCall != null) call.reuseCallsPerHostFrom(existingCall)
      }
    }
    promoteAndExecute()
  }


 private fun promoteAndExecute(): Boolean {
    this.assertThreadDoesntHoldLock()

    val executableCalls = mutableListOf<AsyncCall>()
    val isRunning: Boolean
    synchronized(this) {
      val i = readyAsyncCalls.iterator()
      while (i.hasNext()) {
        val asyncCall = i.next()

        if (runningAsyncCalls.size >= this.maxRequests) break // Max capacity.
        if (asyncCall.callsPerHost.get() >= this.maxRequestsPerHost) continue // Host max capacity.

        i.remove()
        asyncCall.callsPerHost.incrementAndGet()
        executableCalls.add(asyncCall)
        runningAsyncCalls.add(asyncCall)  // 从准备队列中取出来放入运行队列中
      }
      isRunning = runningCallsCount() > 0
    }

    for (i in 0 until executableCalls.size) {
      val asyncCall = executableCalls[i]
      asyncCall.executeOn(executorService)  // executorService是一个缓存线程池
    }

    return isRunning
  }


  fun executeOn(executorService: ExecutorService) {
      client.dispatcher.assertThreadDoesntHoldLock()

      var success = false
      try {
        executorService.execute(this) // this是一个runnable，因此看当前类的run()
        success = true
      } catch (e: RejectedExecutionException) {
        val ioException = InterruptedIOException("executor rejected")
        ioException.initCause(e)
        noMoreExchanges(ioException)
        responseCallback.onFailure(this@RealCall, ioException)
      } finally {
        if (!success) {
          client.dispatcher.finished(this) // This call is no longer running!
        }
      }
    }


    override fun run() {
      threadName("OkHttp ${redactedUrl()}") {
        var signalledCallback = false
        timeout.enter()
        try {
          val response = getResponseWithInterceptorChain() // 责任链设计，构造拦截器链
          signalledCallback = true
          responseCallback.onResponse(this@RealCall, response)
        } catch (e: IOException) {
          if (signalledCallback) {
            // Do not signal the callback twice!
            Platform.get().log("Callback failure for ${toLoggableString()}", Platform.INFO, e)
          } else {
            responseCallback.onFailure(this@RealCall, e)
          }
        } catch (t: Throwable) {
          cancel()
          if (!signalledCallback) {
            val canceledException = IOException("canceled due to $t")
            canceledException.addSuppressed(t)
            responseCallback.onFailure(this@RealCall, canceledException)
          }
          throw t
        } finally {
          client.dispatcher.finished(this)
        }
      }
    }

```

4. 责任链的处理过程看[okhttp](/source/okhttp.md)


