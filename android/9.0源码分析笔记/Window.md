# Window源码分析

参考：

> 《Android开发艺术探索》

## Window的创建过程

### Toast的Window创建过程

#### Toast的视图创建方法

* 视图对应的是一个View成员变量mNextView
* 默认会创建一个com.android.internal.R.layout.transient\_notification的布局，里面的TextView的id为R.id.message

#### Toast的控制

* 使用show\(\)和cancel\(\)开控制显示和隐藏，倒计时隐藏采用内置的handler
* show和cancel内部是通过IPC调用NotificationManagerService来处理



