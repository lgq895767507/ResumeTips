## app卡顿监控

1. 判定应用流畅度的指标

* FPS: 大约60帧/s
* 丢帧: 在16.6ms完成工作却因各种原因没做完，占了后n个16.6ms的时间，相当于丢了n帧
* 流畅度: VSync机制中1s内Loop运行的次数


2. [blockCanary](https://www.jianshu.com/p/914520f1ea03)

* BlockCanary是国内开发者MarkZhai开发的一套性能监控组件,主要通过监控Handler中的dispatchMessage过程所消耗的时间是否超过阀值来判断是否发生卡顿。
* 界面卡顿主要是因为消息分发处理的不及时导致的,Android的消息分发机制主要是由Message/Looper/Handler

```java

public static void loop() {
    ...
    for (;;) {
        ...
        //默认为null，可通过setMessageLogging()方法来指定输出，用于debug功能
        Printer logging = me.mLogging;
        if (logging != null) {
            //事件分发之前的时间T1
            logging.println(">>>>> Dispatching to " + msg.target + " " +
                    msg.callback + ": " + msg.what);
        }
        msg.target.dispatchMessage(msg);
        if (logging != null) {
            //事件分发之后的时间T2
            logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
        }
        ...
    }
}

```

3. systrace+函数插桩
4. 自定义Printer

