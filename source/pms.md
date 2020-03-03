 #### pms简述

 * 用来对apk进行安装、解析、删除、卸载，对外提供信息查询功能。
  * 继承自IPackageManager.Stub
  * IPackageManager提供PackageManager()业务函数，Context.getPackageManager()-->实际上是获得ApplicationPackageManager()对象，通过ApplicationPackageManager的构造函数将 mPM = pm

```java

//main方法主要做了两件事，一个是创建PMS对象，另一个是将PMS注册到ServiceManager中。

 public static PackageManagerService main(Context context, Installer installer,
            boolean factoryTest, boolean onlyCore) {
        PackageManagerServiceCompilerMapping.checkProperties();
        PackageManagerService m = new PackageManagerService(context, installer,
                factoryTest, onlyCore);
        m.enableSystemUserPackages();
        ServiceManager.addService("package", m);
        return m;
    }

```
 * pms执行的步骤大致如下：
   1. 分析之前的准备工作(创建目录，初始化权限，初始化对象等等)
   2. 分析扫描Package，PackageParse负责分析APK文件的解析(分析APK中的androidMainfest.xml),版本升级判断是会去分析一个package.xml文件
   3. 会存储所有的mActivities,mServices,mReceivers...(arraylist集合)
   4. install 安装
      * 将apk复制到手机中，/data/local/temp, 如果是sd卡，则安装在/sdcard/tmp
      * 执行pm_command()函数，调用pm脚本
      * 删除/data/local/temp下的apk文件，因为安装过程中会将apk复制一份到/data/app目录下
      * 检查UID，检查存储空间，确认安装提示等一系列工作 