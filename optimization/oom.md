## 内存oom

* 可行措施：参照快手KOOM, 抖音Liko

#### [oom可能出现的场景](https://zhuanlan.zhihu.com/p/79355050)



#### 分析案例：

```java

┬───
│ GC Root: Global variable in native code
│
├─ dalvik.system.PathClassLoader instance
│    Leaking: NO (ImagePipelineFactory↓ is not leaking and A ClassLoader is never leaking)
│    ↓ PathClassLoader.runtimeInternalObjects
├─ java.lang.Object[] array
│    Leaking: NO (ImagePipelineFactory↓ is not leaking)
│    ↓ Object[].[10338]
├─ com.facebook.imagepipeline.core.ImagePipelineFactory class
│    Leaking: NO (a class is never leaking)
│    ↓ static ImagePipelineFactory.sInstance
│                                  ~~~~~~~~~
├─ com.facebook.imagepipeline.core.ImagePipelineFactory instance
│    Leaking: UNKNOWN
│    ↓ ImagePipelineFactory.mProducerSequenceFactory
│                           ~~~~~~~~~~~~~~~~~~~~~~~~
├─ com.facebook.imagepipeline.core.ProducerSequenceFactory instance
│    Leaking: UNKNOWN
│    ↓ ProducerSequenceFactory.mNetworkFetchSequence
│                              ~~~~~~~~~~~~~~~~~~~~~
├─ com.facebook.imagepipeline.producers.BitmapMemoryCacheGetProducer instance
│    Leaking: UNKNOWN
│    ↓ BitmapMemoryCacheGetProducer.mInputProducer
│                                   ~~~~~~~~~~~~~~
├─ com.facebook.imagepipeline.producers.ThreadHandoffProducer instance
│    Leaking: UNKNOWN
│    ↓ ThreadHandoffProducer.mInputProducer
│                            ~~~~~~~~~~~~~~
├─ com.facebook.imagepipeline.producers.BitmapMemoryCacheKeyMultiplexProducer instance
│    Leaking: UNKNOWN
│    ↓ BitmapMemoryCacheKeyMultiplexProducer.mMultiplexers
│                                            ~~~~~~~~~~~~~
├─ java.util.HashMap instance
│    Leaking: UNKNOWN
│    ↓ HashMap.table
│              ~~~~~
├─ java.util.HashMap$Node[] array
│    Leaking: UNKNOWN
│    ↓ HashMap$Node[].[4]
│                     ~~~
├─ java.util.HashMap$Node instance
│    Leaking: UNKNOWN
│    ↓ HashMap$Node.value
│                   ~~~~~
├─ com.facebook.imagepipeline.producers.MultiplexProducer$Multiplexer instance
│    Leaking: UNKNOWN
│    ↓ MultiplexProducer$Multiplexer.mConsumerContextPairs
│                                    ~~~~~~~~~~~~~~~~~~~~~
├─ java.util.concurrent.CopyOnWriteArraySet instance
│    Leaking: UNKNOWN
│    ↓ CopyOnWriteArraySet.al
│                          ~~
├─ java.util.concurrent.CopyOnWriteArrayList instance
│    Leaking: UNKNOWN
│    ↓ CopyOnWriteArrayList.elements
│                           ~~~~~~~~
├─ java.lang.Object[] array
│    Leaking: UNKNOWN
│    ↓ Object[].[7]
│               ~~~
├─ android.util.Pair instance
│    Leaking: UNKNOWN
│    ↓ Pair.first
│           ~~~~~
├─ com.facebook.imagepipeline.datasource.AbstractProducerToDataSourceAdapter$1 instance
│    Leaking: UNKNOWN
│    Anonymous subclass of com.facebook.imagepipeline.producers.BaseConsumer
│    ↓ AbstractProducerToDataSourceAdapter$1.this$0
│                                            ~~~~~~
├─ com.facebook.imagepipeline.datasource.CloseableProducerToDataSourceAdapter instance
│    Leaking: UNKNOWN
│    ↓ CloseableProducerToDataSourceAdapter.mSubscribers
│                                           ~~~~~~~~~~~~
├─ java.util.concurrent.ConcurrentLinkedQueue instance
│    Leaking: UNKNOWN
│    ↓ ConcurrentLinkedQueue.head
│                            ~~~~
├─ java.util.concurrent.ConcurrentLinkedQueue$Node instance
│    Leaking: UNKNOWN
│    ↓ ConcurrentLinkedQueue$Node.item
│                                 ~~~~
├─ android.util.Pair instance
│    Leaking: UNKNOWN
│    ↓ Pair.first
│           ~~~~~
├─ com.bilibili.lib.image.FrescoImageLoader$1 instance
│    Leaking: UNKNOWN
│    Anonymous subclass of com.facebook.datasource.BaseDataSubscriber
│    ↓ FrescoImageLoader$1.val$listener
│                          ~~~~~~~~~~~~
├─ com.bilibili.bililive.videoliveplayer.ui.widget.LiveCardCornerV2$bind$1 instance
│    Leaking: UNKNOWN
│    Anonymous class implementing com.bilibili.lib.image.ImageLoadingListener
│    ↓ LiveCardCornerV2$bind$1.this$0
│                              ~~~~~~
├─ com.bilibili.bililive.videoliveplayer.ui.widget.LiveCardCornerV2 instance
│    Leaking: YES (View.mContext references a destroyed activity)
│    mContext instance of com.bilibili.lib.ui.GeneralActivity with mDestroyed = true
│    View#mParent is set
│    View#mAttachInfo is null (view detached)
│    View.mID = R.id.corner_right_top
│    View.mWindowAttachCount = 1
│    ↓ LiveCardCornerV2.mContext
├─ com.bilibili.lib.ui.GeneralActivity instance
│    Leaking: YES (LiveCardCornerV2↑ is leaking and Activity#mDestroyed is true)
│    ↓ GeneralActivity.mFragments
├─ androidx.fragment.app.FragmentController instance
│    Leaking: YES (GeneralActivity↑ is leaking)
│    ↓ FragmentController.mHost
├─ androidx.fragment.app.FragmentActivity$HostCallbacks instance
│    Leaking: YES (GeneralActivity↑ is leaking)
│    ↓ FragmentActivity$HostCallbacks.mFragmentManager
├─ androidx.fragment.app.FragmentManagerImpl instance
│    Leaking: YES (GeneralActivity↑ is leaking)
│    ↓ FragmentManagerImpl.mNonConfig
╰→ androidx.fragment.app.FragmentManagerViewModel instance
​     Leaking: YES (ObjectWatcher was watching this because androidx.fragment.app.FragmentManagerViewModel received ViewModel#onCleared() callback)
​     key = b2205c62-4548-4405-a4db-ee72aa40fd14
​     watchDurationMillis = 30058
​     retainedDurationMillis = 25054

```

#### 分析链路
* 从下往上看，最上面一行是 GC Root。GC Root的定义可以自己查查。说明最上面是根对象，往下是逐渐到我们真正泄露的对象；

#### 分析方法
##### 基本知识
1. 泄露对象：一般是Activity, Fragment, ViewModel以及Fragment的mView；
2. 泄露时机：Activity的destroy, Fragment的destroyView以及destroy， ViewModel的onCleared

#### 排查问题


确认泄露时机：最后一个对象的描述：一般为xxx received xxx callback, 比如上面的ObjectWatcher was watching this because androidx.fragment.app.FragmentManagerViewModel received ViewModel#onCleared() callback；
确认泄露对象：从下往上找最近一个我们包名下的对象，这边就是com.bilibili.bililive.videoliveplayer.ui.widget.LiveCardCornerV2 instance；
每个对象下面都会有有一些描述。关键属性的值，比如Activity#mDestroyed is true 代表Activity还没有调用onDestroy();
排查链路，对照代码；


#### 非静态内部类隐式持有外部类引用
##### Handler场景
* 总结：Handler有延时任务的情况下， 如果不能很好管理延时任务的移除，需要在对应的时机移除。
* 不建议使用WeakReference，容易被GC了。
* 对应时机：
1. 操作View，那么时机就是 自定义view的onDetachFromWindow，Activity的onDestroy, Fragment的onDestroyView；
2. 只是内部类隐式持有外部Context，那就Activity的onDestroy，Fragment的onDestroy；

##### 匿名内部类
* 总结：实现Callback、Listener时候，需要及时的Remove。
* 不建议Fragment，Activity，View直接实现接口，当做Callback传递。
* 场景比如：动画addListener, 广播的Register，RxJava的Subscribe都需要及时(对应的生命周期)的remove。

##### CharSequence的文案的保存
* 总结：如果需要保存SpannableString，EditText之类的CharSequence文案，建议toString()再保存。因为部分CharSequence会包含Span, 而部分Span如ClickSpan就会隐式的包含Context。
* 场景：输入法的文案保存，弹幕区的缓存弹幕消息，都会默认包含Context。

##### 外部直接持有引用, 无法释放
* 总结：具有生命周期的对象尽量不要直接持有，除非在其生命周期结束的时候，手动移除。
* 场景：比如Activity会直接持有Fragment，将其作为一个全局变量持有，大部分场景没有问题，因为fragment的生命周期和Activity一致。但是有的动态添加的，比如viewpager内的Fragment
* 如果被Activity持有，很容易出现泄露。建议使用FragmentManager实时获取，随用随取。


##### Fragment的mView泄露
* 总结：随着升级andriodx，Fragment的mView具有自己的生命周期，部分和view生命周期绑定的事件（比如网络请求改变ui），livedata的监听使用getViewLifecycleOwner(), 时机上搭配getViewLifecycleOwnerLiveData()配套使用更加nice。
* 场景：一般在ViewPager场景中比较常见，Fragment切换时候，会引起Fragment的onDestoryView()（临近的切换），此时Fragment没有被销毁（为了下次切换回来复用），但是view被销毁了。
* 重新复用的时候，重新onCreateView, 创建新的对象。
* 如果view传递给了其他对象，当onDestoryView调用的时候，销毁mView对象，但是view被其他对象持有，于是无法释放，所以泄露。自定义view内部实现匿名内部类需要注意！

