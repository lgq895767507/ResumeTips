#### Java类加载过程

* 生命周期：加载，验证，准备，解析，初始化，使用，卸载。
*  验证，准备，解析三个部分统称为连接
* 双亲委派模型。Bootstrap ClassLoader -> Extensino ClassLoader -> Application ClassLoader -> User ClassLoader

#### 以下有且只有5种情况会初始化类（如果类没有被初始化，则需要先触发其初始化）
1. 遇到new,getstatic,putstatic,invokestatic这四条指令的时候
2. 使用java.lang.reflect包的方法对类进行反射调用的时候
3. 当初始化一个类的时候，如果发现其父类还没有进行初始化，则会先触发其父类的初始化
4. 当虚拟器启动，需要指定一个执行的主类(包含main()的那个类)
5. 当使用JDK1.7的动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后解析的结果是REF_getStatic,REF_putStatic,REF_invokeStatic的方法句柄