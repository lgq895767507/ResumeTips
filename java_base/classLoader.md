#### Java类加载过程

* 生命周期：加载，验证，准备，解析，初始化，使用，卸载。
*  验证，准备，解析三个部分统称为连接
* 双亲委派模型。Bootstrap ClassLoader -> Extension ClassLoader -> Application ClassLoader -> User ClassLoader

#### 以下有且只有5种情况会初始化类（如果类没有被初始化，则需要先触发其初始化）
1. 遇到new,getstatic,putstatic,invokestatic这四条指令的时候
2. 使用java.lang.reflect包的方法对类进行反射调用的时候
3. 当初始化一个类的时候，如果发现其父类还没有进行初始化，则会先触发其父类的初始化
4. 当虚拟器启动，需要指定一个执行的主类(包含main()的那个类)
5. 当使用JDK1.7的动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后解析的结果是REF_getStatic,REF_putStatic,REF_invokeStatic的方法句柄

GCRoot的对象包括：
1. 虚拟机栈中引用的对象
2. 方法区中类静态属性引用的对象
3. 方法区中常量引用的对象
4. 本地方法栈中JNI(一般说的native方法)引用的对象


#### 插件化
##### [Android插件化的实现原理](https://jsonchao.github.io/2018/01/12/Android%20%E6%8F%92%E4%BB%B6%E5%8C%96/)

* Android中有两种类加载器，DexClassLoader和PathClassLoader，它们都继承于BaseDexClassLoader。
* 两者的区别：DexClassLoader多了一个optimizedDirectory的路径参数，这个目录必须是内部存储路径，用于缓存系统创建的Dex文件。所以我们可以使用DexClassLoader去加载外部Apk中的类。

> DexClassLoader：能够加载未安装的jar/apk/dex 
> PathClassLoader：只能加载系统中已经安装过的apk


