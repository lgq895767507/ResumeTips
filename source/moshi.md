## Moshi

* [原理分析对比](https://www.jianshu.com/p/4e206ee7dca0)
* 替代gson，更好的支持kotlin

* 反射/注解

#### 总结 

* 由于Moshi底层的IO操作采用的是Okio，所以在序列化的时候性能优于Gson及KS以及其它框架，这个是很好理解的，在反序列化的过程中，我们看到Moshi的解析效率跟Kotlin的官方序列化工具基本持平，但是稍快于Gson，本次测试中没有把Moshi创建Adapter的时间计算在内，因为他是可以单独创建作为一个单例，跟解析保持相互独立，跟前面提到的最优解保持一致。



