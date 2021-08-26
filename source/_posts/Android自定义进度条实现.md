---
title: Android自定义进度条实现
date: 2020-2-26 10:6:25
tags:
- Android
- 进度条
- 自定义view
---

#### xml布局代码
```
android:id=@+id/qrcode_loading_bar;	
android:layout_gravity=center;	
android:indeterminateDrawable=@drawable/qrcode_loading_drawable;
android:indeterminateDuration=2000;
```
#### 自定义drawable qrcode_loading_drawable.xml
```
?xml version=1.0 encoding=utf-8
<rotate xmlns:android=&quot;http://schemas.android.com/apk/res/android>
android:fromDegrees=0;  
android:pivotX=50%;
android:pivotY=50%;
android:toDegrees=360;
android:drawable=@drawable/qrcode_cricle_loading
</rotate>
```
![](a.png)