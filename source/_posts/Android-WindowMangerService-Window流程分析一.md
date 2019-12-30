---
title: Android WindowMangerService Window流程分析一
date: 2019-11-29 14:13:26
tags:
- Android
- window
- 源码分析
---

先从安卓系统启动开始 -- 源码参考android sdk29

### SystemServer启动流程

Android本质上是基于linux操作系统，理论上大值启动过程与linux差不多，不同的是Android采用的是嵌入式设备，没有PC上的BIOS等设备，取而代之的是叫做Bootloader的系统启动加载器，用于初始化硬件设备，为加载内核做好准备。

1️⃣ 按下电源开机通电 --> 2⃣️ 加载引导程序Bootloader到内存 --> 3⃣️ Bootloader拉起linux内核 --> 4⃣️ 启动系统进程 --> 5⃣️ 第一个用户空间进程 init进程
  
<font bgcolor=yellow><b>Bootloader:</b></font>  引导程序并不隶属于 Android 系统，它的作用是初始化硬件设备，加载内核文件等，为系统内核启动搭建好所需的环境。Bootloader 引导程序是 Android 系统开机运行的第一个程序，它是针对特定的主板与芯片的，因此，对于不同的设备制造商，它们的引导程序都是不同的。当前比较受欢迎的引导程序有 uboot(现有整机使用)，redboot 等。

<font bgcolor=yellow><b>Linux内核:</b></font>  Linux 内核开始启动，初始化各种软硬件环境，加载驱动程序，挂载根文件系统等，最重要的是，内核启动完成后，它会在系统文件中寻找 ”init” 文件，然后启动 init 进程。到此，Android 新世界正式开启。

<font bgcolor=yellow><b>init进程:</b></font>  Linux用户空间的第一个进程，进程号固定为 1，PID始终为1。init 进程有两个责任，一是挂载目录，比如/sys、/dev、/proc，二是运行 init.rc 脚本（init 进程在解析 init.rc 脚本时，会创建 Zygote 进程）
![](1.png)

<font bgcolor=yellow><b>Zygote进程:</b></font>  init 进程在解析 init.rc 脚本时，会创建 Zygote 进程，只有该进程才会建立起真正的 Android 运行空间，它是 Android 系统最重要的进程之一。后续 Android 中的应用进程都是由 Zygote 进程 Fork 出来的，因此，Zygote 是 Android 系统所有应用的父进程（即 Java 进程的鼻祖）

“所有的Java应用程序进程及系统服务SystemServer进程都由Zygote进程通过fork孵化而来”

init.zygote64_32.rc
```
service zygote /system/bin/app_process64 -Xzygote /system/bin --zygote --start-system-server --socket-name=zygote
    class main
    priority -20
    user root
    group root readproc
    socket zygote stream 660 root system
...
```
Zygote由ini进程通过解析init.rc调用app_process64可执行文件，生成对应的zygote进程，具体位置
frameworks/base/cmds/app_process/app_main.cpp
![](2.png)
**？**zygote既然由app_process启动，进程为什么不叫app_process，却叫zygote？
```
app_main.cpp#main    
//niceName不空，比如zygote进程。
if (!niceName.isEmpty()) {
     //此处把app_process进程名，改为zygote，见 注② 这就是上述说得改名操作
     runtime.setArgv0(niceName.string(), true /* setProcName */);
}
```
另外一点是app_process可以直接启动jar包，如monkey的启动脚本，vysor的录屏等都是采用了此方式
```
/system/bin/monkey
base=/system
export CLASSPATH=$base/framework/monkey.jar
exec app_process $base/bin com.android.commands.monkey.Monkey "$@"
```
*zygote创建了Android的java世界，因此zygote必须完成JVM虚拟机的创建，然后孵化出java世界的第一个进程SystemServer*

#### zygote分类
native:  JVM虚拟机 注册JNI函数                    // 开始跑java代码
java层:  ZygoteInit#main() fork出systemServer 预加载需要的类和资源
             ZygoteInit#main() -> zygoteServer.runSelectLoop(..) 等待socket连接 fork出apk程序进程
                   
#### SystemServer进程:   
SystemServer 的主要的作用是启动各种系统服务，比如 ActivityManagerService，PackageManagerService，WindowManagerService 以及硬件相关的 Service 等服务，我们平时熟知的各种系统服务其实都是在 SystemServer 进程中启动的
![](3.png)

Android 平台的主要组件
![](4.png)

### WindowManagerService启动

<font bgcolor=orange><b>创建过程:</b></font> SystemServer#startOtherServices()，通过调用WindowManagerService#main()返回自己的静态实例 
```
private void startOtherServices() {
     ......
     WindowManagerService wm = null;
     ......

     wm = WindowManagerService.main(context, inputManager,
                    mFactoryTestMode != FactoryTest.FACTORY_TEST_LOW_LEVEL...);

     ServiceManager.addService(Context.WINDOW_SERVICE, wm); //注册服务

     ......

     mActivityManagerService.setWindowManager(wm); //和ActivityManagerService进行关联


     ......

     try {
         wm.displayReady();  //初始化显示信息，尺寸，位置等
     } catch (Throwable e) {
        reportWtf("making display ready", e);

     }

     ......

     try {
         wm.systemReady(); //通知WMS系统的初始化工作已完成
     } catch (Throwable e) {
         reportWtf("making Window Manager Service ready", e);

     }

     ......

}
```

#### S端存储: 
通过ServiceManager BinderInternal#public static final native IBinder getContextObject(); 拿到binder  -> 把实例存到Native层的ServiceManager

#### C端获取: 
ContextImpl SystemServiceRegistry#static代码中通过ServiceManager获取所有service存到hashmap缓存



#### WMS重要成员
// appWindowToken 应用窗口token 如果是系统窗口则同windowToken 没有应用窗口就是null
// windowToken 窗口token 根据token判断哪些窗口要一起管理
    - AppWindowToken extend WindowToken

// 代表一个活动的client会话
// 对应每个进程的单例对象WindowMangaerGlobal
    - Session

// 与显示的窗口对应 持有WindowToken,Session,IWindow等对象
    - WindowState
![](5.jpg)
// 提供所有UI行为的管理 如window层级控制
WindowManagerPolicy mPolicy;

// 管理动画
WindowAnimator mAnimator;

// 设备窗口层次结构的根结点 继承WindowContainer<DisplayContent-对应一个抽象意义上的显示屏 持有windowState>.
RootWindowContainer mRoot;



### Window基本原理

#### 基本概念 -  
所有视图的基石，在Android中Window表示一个窗口，我们日常使用的activity就会绑定一个window实例，用来管理activity中的相关视图
                    抽象类 唯一实现是PhoneWindow 
                 
#### 类别 -  
应用Window 对应一个Activity
           子Window 依附在父Window中
           系统Window 需要声明权限  <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
             6.0以上的 动态获取        

![](6.png)

Activity中持有WindowManager与Window的引用，Window通过直接new PhoneWindow得到，WindowManager通过Window提供的接口取得
PhoneWindow中有一系列的成员变量，一般是各种回调，是通过Activity直接设置过来的，都是Activity相同实例。然后Activity通过Window的方法实现初始化Window中的成员变量WindowManager
WindowManager通过上述方法，在创建Window成员变量WindowManager的同时，把Window设置给WindowManager实现双向关联

![](7.png)

#### 创建过程 - 
Activity  在ActivityThread#performLaunchActivity  Activity#attach(..)   new PhoneWindow()
                   在phoneWindow中创建decorView 如果mPendingRemoveWindow不为空 复用decorView token
                   在ActivityThread#handleResumeActivity(...) 将mDecor和属性add到wm -> Activity#makeVisible()设置视图可见
                   
Dialog Dialog#dialog()  new PhoneWindow()  需要传入activity的context拿到token
                   也是通过 PhoneWindow#setContentView()  设置视图
                   在show方法中将mDecor和属性加到wm 
                   
PopUpWindow#showAsDropDown(View chchor)  通过传入的view拿到token

Toast 系统window  层级 WindowManager.LayoutParams.TYPE_TOAST  +5

#### 组成 - 
顶层view DecorView extends FrameLayout 
             PhoneWindow#setContentView()  设置视图到 ViewGroup mContentParent

#### 添加过程 -  
调用wm的addView(View v, LayoutParams p)  -> WindowManagerGlobal#addView(v, p, mParentWindow) 
                      -> ViewRootImpl#setView(...) -> Session#addToDisplay(...) ->  WindowManagerService#addWindow(...)                                WindowManagerGlobal 每个进程都只有一个 实现window的三大操作
                      ViewRootImp 每个window都有个 复制View的绘制
                      Session 实现WindowSession  和windowManagerService通信


#### window 层级

##### 应用窗口

      应用程序窗口。
      public static final int FIRST_APPLICATION_WINDOW = 1;
 
      所有程序窗口的“基地”窗口，其他应用程序窗口都显示在它上面。     
      public static final int TYPE_BASE_APPLICATION   =1;       
      ...
      应用程序窗口结束。
      public static final int LAST_APPLICATION_WINDOW = 99;

##### 子窗口层级
      ...
      面板窗口，显示于宿主窗口上层。
      public static final int TYPE_APPLICATION_PANEL  = FIRST_SUB_WINDOW;
      ...

      应用程序窗口的子面板。显示于所有面板窗口的上层。（GUI的一般规律，越“子”越靠上）
      public static final int TYPE_APPLICATION_SUB_PANEL = FIRST_SUB_WINDOW +2;
       ...

      媒体信息。显示在媒体层和程序窗口之间，需要实现透明（半透明）效果。（例如显示字幕）
      public static final int TYPE_APPLICATION_MEDIA_OVERLAY  = FIRST_SUB_WINDOW +4;

      子窗口结束。（ End of types of sub-windows ）
      public static final int LAST_SUB_WINDOW        = 1999;

##### 系统窗口层级

      系统窗口。非应用程序创建。
     public static final int FIRST_SYSTEM_WINDOW    = 2000;
      ...

      系统提示。它总是出现在应用程序窗口之上。
      public static final int TYPE_SYSTEM_ALERT      =  FIRST_SYSTEM_WINDOW +3;

      锁屏窗口。
      public static final int TYPE_KEYGUARD          = FIRST_SYSTEM_WINDOW +4;
      ...

      系统对话框。（例如音量调节框）。
      public static final int TYPE_SYSTEM_DIALOG     =  FIRST_SYSTEM_WINDOW +8;

      锁屏时显示的对话框。
      public static final int TYPE_KEYGUARD_DIALOG   =  FIRST_SYSTEM_WINDOW +9;

      系统内部错误提示，显示于所有内容之上。
      public static final int TYPE_SYSTEM_ERROR      =  FIRST_SYSTEM_WINDOW +10;
      ...

      系统窗口结束。     public static final int LAST_SYSTEM_WINDOW     = 2999;


#### window flag

// 该窗口后面的所有内容都会变暗。
public static final int FLAG_DIM_BEHIND = 0x00000002;

// 该窗口将永远不会获得关键的输入焦点，不拦截键或其他按钮事件
// 开启这个默认会开启FLAG_NOT_TOUCH_MODAL
public static final int FLAG_NOT_FOCUSABLE = 0x00000008;

// 不拦截window之外的点击事件
public static final int FLAG_NOT_TOUCH_MODAL = 0x00000020;


#### 新系统特性 特殊权限
安卓8 26 O 开始 系统弹窗用这个 WindowManager.LayoutParams.TYPE_APPLICATION_OVERLAY
             但是26之前不支持这个属性
 安卓6 23 M 以后权限需要动态获取 <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>

// +8 层级往上不能直接用
WindowManager.LayoutParams.TYPE_SYSTEM_DIALOG
会报异常 permission denied for window type 2008
- 例外 WindowManager.LayoutParams.TYPE_SYSTEM_ERROR +10 这个可以用


#### 特殊方式获取和判断
```
private static final int REQUEST_CODE = 1; 
private void requestAlertWindowPermission() {     
    Intent intent = new Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION);
    intent.setData(Uri.parse("package:" + getPackageName()));
    startActivityForResult(intent, REQUEST_CODE); 
} 

@Override 
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
   super.onActivityResult(requestCode, resultCode, data);
   if (requestCode == REQUEST_CODE) {
     if (Settings.canDrawOverlays(this)) {
       Log.i(TAG, "onActivityResult granted");
     } else {
       Log.i(TAG, "onActivityResult: not granted");
     }
   }
}
```

#### 常用操作1 弹出某个自定义view 

```
/*
* 弹出系统弹窗 不需要activity的context
*/
public void popSystemWindow() {
    WindowManager windowManager = (WindowManager) getSystemService(WINDOW_SERVICE);
    WindowManager.LayoutParams layoutParams = new WindowManager.LayoutParams();
    layoutParams.type = WindowManager.LayoutParams.TYPE_SYSTEM_ALERT;
    layoutParams.height = 100;
    layoutParams.width = 100;
    layoutParams.x = Gravity.CENTER;
    layoutParams.y = Gravity.CENTER;
    final Button button = new Button(this);
    button.setBackground(getResources().getDrawable(R.drawable.launcher));
    windowManager.addView(button, layoutParams);
}
```

#### 常用操作2 给弹出的view添加蒙层效果

```
/*
* 给view加类似dialog的蒙层
* 蒙层的type层级要低于view的层级
*/
private void addMask(IBinder token) {
    WindowManager.LayoutParams wl = new WindowManager.LayoutParams();
    wl.width = WindowManager.LayoutParams.MATCH_PARENT;
    wl.height = WindowManager.LayoutParams.MATCH_PARENT;
    wl.format = PixelFormat.TRANSLUCENT; // 不设置这个弹出框的透明遮罩显示为黑色
    wl.type = WindowManager.LayoutParams.TYPE_APPLICATION_PANEL;
    wl.token = token; // 获取当前Activity中的View中的token,来依附Activity
    maskView = new View(mContext);
    maskView.setBackgroundColor(Color.parseColor("#4D000000"));
    maskView.setFitsSystemWindows(false);
    maskView.setOnKeyListener((v, keyCode, event) -> {
        if (keyCode == KeyEvent.KEYCODE_BACK) {
            removeMask();
        }
        return false;
    });
    maskView.setOnClickListener(v -> {
        removeMask();
    });
    windowManager.addView(maskView, wl);
}

/*
* 移除蒙层
*/
private void removeMask() {
    if (null != maskView) {
        windowManager.removeViewImmediate(maskView);
        maskView = null;
    }
}
```
也可以直接拿到activity的window设置flag WindowManager.LayoutParams.FLAG_DIM_BEHIND
但对activity过于复杂的应用不适用 例如note

####  常用操作3 调整视图层级

如提高dialog层级到系统层级 dialog.getWindow().setType(LayoutParams.TYPE_SYSTEM_ALERT);
但是要注意
要声明权限<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
6.0以上的 动态获取
8.0换成TYPE_APPLICATION_OVERLAY























