#### android常见问题

1. jetpack你了解哪些？
   * livedata
   * room
   * viewmodel
   * lifecycle
   * compose
   * datastore
   * databinding

2. app如何防抓包
   * 加固：

3. 国际化适配对于中东地区的如何适配？

4. 内存优化如何分析定位的？

5. app启动优化具体做了什么？多进程的情况下会执行几次application? 

6. [sgva和lottie的对比](https://jfson.github.io/2018/01/08/41-anim/)

7. Android中Bitmap内存优化

> Bitmap内存占用 ≈ 像素数据总大小 = 横向像素数量 × 纵向像素数量 × 每个像素的字节大小 （ARGB_4444占用字节大小为4）,或者是 Bitmap内存占用 ≈ 像素数据总大小 = 图片宽 × 图片高× (设备分辨率/资源目录分辨率)^2 × 每个像素的字节大小
   
   * 减少内存主要可以通过以下几种方式：
   1. 使用低色彩的解析模式，如RGB565，减少单个像素的字节大小
   2. 资源文件合理放置，高分辨率图片可以放到高分辨率目录下
   3. 图片缩小，减少尺寸

8. 超大图加载（BitmapRegionDecoder）
9. 跨进程传递大图 (Bundle#putBinder)
   * 自定义对象继承Binder类，然后bundle.putBinder("key", MyBinder("desc"));
   * 较大的bitmap直接通过Intent传递 是因为Intent启动组件时，系统禁掉了文件描述符fd,bitmap无法利用共享内存，只能采用拷贝到缓冲区的方式，导致缓冲区超限,触发异常;putBinder 的方式，避免了intent 禁用描述符的影响，bitmap 写parcel时的fd 默认是true,可以利用到共享内存，所以能高效传输图片。


10. aActivity跳转bActivity的生命周期的执行？如何让aActivity不执行onStop()方法？

```java

2021-03-01 21:46:33.391 19417-19417/com.lewis.android_test I/lgq: MainActivity--onCreate
2021-03-01 21:46:33.399 19417-19417/com.lewis.android_test I/lgq: MainActivity--onStart
2021-03-01 21:46:33.400 19417-19417/com.lewis.android_test I/lgq: MainActivity--onResume
2021-03-01 21:46:33.411 19417-19417/com.lewis.android_test I/lgq: MainActivity--onPause
2021-03-01 21:46:33.487 19417-19417/com.lewis.android_test I/lgq: MainActivity2--onCreate
2021-03-01 21:46:33.490 19417-19417/com.lewis.android_test I/lgq: MainActivity2--onStart
2021-03-01 21:46:33.490 19417-19417/com.lewis.android_test I/lgq: MainActivity2--onResume
2021-03-01 21:46:33.411 19417-19417/com.lewis.android_test I/lgq: MainActivity--onStop

```

如果要让aActivity不执行onStop方法，只要设置bActivity的theme为透明即可:android:theme="@android:style/Theme.Translucent"

11. intent为什么传自定义对象需要序列化？
    * 因为Intent启动会跨进程，和AMS的进程通信，所携带的数据要能够在不同进程间传输，不同进程之间的java对象是无法传输，所以我们此处要对对象进行序列化。
    
12. http上传图片的流程？
    * 
13. application的onCreate什么时候执行的？除了刚启动的时候，还会有可能被执行吗？多进程如何设置？
14. [recycleView性能优化](https://www.cnblogs.com/zgz345/p/13436005.html)

