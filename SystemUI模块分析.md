---
title: SystemUI模块分析
date: 2018-03-26 23:22:11
categories: Android
tags: 
    - android 
    - systemui
---
## 2018/3/26

### 1. CommandQueue类中的setSystemUiVisibility在哪里调用？

- CommandQueue继承自IStatusBar.Stub，是IStatusBar的Binder Native端，这个Binder对象的Binder Proxy端将会保存在IStatusBarService中，
而StatusBarManagerService继承自IStatusBarService.Stub，这下就把Binder对象的Bn端和Bp端搞清楚了。
- 在StatusBarManagerService中有这样一处的调用
`
mBar.setSystemUiVisibility(vis, fullscreenStackVis, dockedStackVis, mask, fullscreenBounds, dockedBounds);
` ，显然这里的调用便链接到CommandQueue中的setSystemUiVisibility()方法。
- StatusBarManagerService中的setSystemUiVisibility()方法的调用则是在NavigationBarTransitions里面触发的，`mBarService.setSystemUiVisibility(0, View.SYSTEM_UI_FLAG_LOW_PROFILE, "LightsOutListener");`，mBarService是通过`IStatusBarService.Stub.asInterface(ServiceManager.getService(Context.STATUS_BAR_SERVICE));`获取到IStausBarService实例。

### 2. setSystemUiVisibility(param)的参数们解析
```java
public static final int SYSTEM_UI_FLAG_VISIBLE = 0;
public static final int SYSTEM_UI_FLAG_LOW_PROFILE = 1;    
public static final int SYSTEM_UI_FLAG_HIDE_NAVIGATION = 2;
public static final int SYSTEM_UI_FLAG_FULLSCREEN = 4;
public static final int SYSTEM_UI_FLAG_LAYOUT_STABLE = 256;
public static final int SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION = 512;
public static final int SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN = 1024;
```
- [View.SYSTEM_UI_FLAG_VISIBLE](https://developer.android.google.cn/reference/android/view/View.html#SYSTEM_UI_FLAG_VISIBLE)：显示状态栏，Activity不全屏显示（恢复到有状态栏的正常情况）。
- [View.SYSTEM_UI_FLAG_LOW_PROFILE](https://developer.android.google.cn/reference/android/view/View.html#SYSTEM_UI_FLAG_LOW_PROFILE)：状态栏显示处于低能显示状态（low profile模式），状态栏上一些图标显示会被隐藏。
- [View.SYSTEM_UI_FLAG_HIDE_NAVIGATION](https://developer.android.google.cn/reference/android/view/View.html#SYSTEM_UI_FLAG_HIDE_NAVIGATION)：隐藏虚拟按键（导航栏）。
- [View.SYSTEM_UI_FLAG_FULLSCREEN](https://developer.android.google.cn/reference/android/view/View.html#SYSTEM_UI_FLAG_FULLSCREEN)：Activity全屏显示，且状态栏会被覆盖掉。
- [View.SYSTEM_UI_FLAG_LAYOUT_STABLE](https://developer.android.google.cn/reference/android/view/View.html#SYSTEM_UI_FLAG_LAYOUT_STABLE)：保持View Layout不变，隐藏状态栏或者导航栏后，View不会拉伸。
- [View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION](https://developer.android.google.cn/reference/android/view/View.html#SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION)：隐藏虚拟按键（导航栏），Activity底端布局部分会被导航栏遮住。
- [View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN](https://developer.android.google.cn/reference/android/view/View.html#SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN)：Activity全屏显示，但状态栏不会被隐藏覆盖，状态栏依然可见，Activity顶端布局部分会被状态栏遮住。
