#### AMS简述
* 负责四大组件的启动、切换、调度及应用进程的管理和调度工作。

##### startActivity

* ActivityManagerService.java::startActivityAndWait(IApplicationThread caller, ...) //IApplicationThread通常是由一个应用进程发起，和AMS进程通信交互的通道
* mMainStack.startActivityMayWait()  //mMainStack是ActivityStack类型,是AMS的成员变量