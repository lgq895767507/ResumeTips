#### AMS简述
* 负责四大组件的启动、切换、调度及应用进程的管理和调度工作。

##### startActivity

* ActivityManagerService.java::startActivityAndWait(IApplicationThread caller, ...) //IApplicationThread通常是由一个应用进程发起，和AMS进程通信交互的通道
* mMainStack.startActivityMayWait()  //mMainStack是ActivityStack类型,是AMS的成员变量


##### ActivityRecord，TaskRecord， ActivityStack 三者之间的关系？

* ActivityRecord对应一个Activity，保存了一个Activity的所有信息
* TaskRecord内部维护一个ArrayList<ActivityRecord>，用来保存ActivityRecord
* ActivityStack内部维护了一个ArrayList<TaskRecord>，用来管理TaskRecord