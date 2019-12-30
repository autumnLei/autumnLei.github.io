---
title: Android在子线程发送Toast
date: 2019-05-17 19:56:46
tags:
- Android
- 线程
---
Toast不能直接在子线程启动 错误java.lang.RuntimeException: Can't toast on a thread that has not called Looper.prepare()
可以拿到UI线程的looper通过hander传过去
```
new Handler(Looper.getMainLooper()).post(new Runnable() {
    @Override
    public void run() {
        Toast.makeText(context, ""+msg, Toast.LENGTH_LONG).show();
    }
});
```