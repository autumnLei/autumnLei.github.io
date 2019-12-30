---
title: 在onCreate()中调用finish()方法
date: 2019-05-06 09:16:11
tags:
- Android
---

#### 在onCreate()中调用finish()方法
生命周期变化 onCreate()->onDestroy();
同时finish()方法后的代码段仍然会执行
```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
        Log.d(TAG, "onCreate: a");
        finish();
        Log.d(TAG, "onCreate: b");
    }
```
输出：
	onCreate: a
	onCreate: b
#### 在onPause(), onStop(), onDestroy()中调用finish()方法
生命周期不变 onCreate()->onPause()->onStop->onDestroy();
注意 -- 方法内finish()后的代码仍会执行 可用return语句打断
