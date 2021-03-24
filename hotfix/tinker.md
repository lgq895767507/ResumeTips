####  tinker
  * 类加载  classloader 子类：BaseDexClassLoader
  * dexPathList类中有一个dexElements数组
  * 思路
  1. 自己创建一个类加载器
  2. 用自己创建的类加载器加载x.dex修复包(得到对应的dexElements[])
  3. 将得到的dexElements[]与系统的dexElements[]合并，并且将自己的放在最前面
  4. 通过反射技术，将合并后的新数组，赋值给pathList 


#### andfix
* class文件打包成dex文件命令操作：
  * dx --dex --output out.dex .  //将当前目录下所有的class文件打包成dex文件 

1. ArtMethod对象
   *  保存着Java方法的所有信息，访问权限，执行地址等，所以如果要替换方法，其实就要把该方法的所有ArtMethod对象的属性全部替换成另外一个就可以了
   *  不同Android版本的Art虚拟机里，Java对象对应底层的数据结构是不同的，因此需要根据不同版本分别处理，分别替换不同的函数。
   *  通过env->FromReflectedMethod得到方法对应的ArtMethod的真正开始地址，然后强转为ArtMethod指针，从而对其所有成员进行修改，但是有些厂商对ArtMethod有修改，就有可能出现失败对情况。

2. Sophix  
   * 由于andfix有版本限制兼容问题，因此sophix是对artmethod整体替换，memcpy(target, meth, sizeof(ArtMethod));

3. dex替换
   * 提供dex差量包，整体替换dex的方案
   * 差量的方式给出patch.dex
   * 然后将patch.dex与应用的classes.dex合并成一个完整的dex
   * 完整dex加载得到dexFile对象作为参数构建一个Element对象
   * 然后整体替换掉旧的dex-Elements数组。