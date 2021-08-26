---
title: broadcastReceiver 注册代码小记
date: 2020-12-16 10:23:28
tags:
- Android
- receiver
---

### 1，动态注册
记得在manifest里面注册 四大组建都得在里面注册
```
public class MyBroadcastReceiver extends BroadcastReceiver{}
    @Override
    public void onReceive(Context context, Intent intetn){
         //do something
    }

}
```

调用
```
MyBroadcastReceiver receiver = new MyBoradcastReceiver();
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE");
registerReceiver(receiver, intetnFilter);
```
释放
```
unregisterReceiver(receiver);
```

### 2，静态注册
接收器类同上
```
<receiver android:name=".MyBroadcastReceiver.">
    <intent-filter>
        <action android:name = "android.intent.cation.BOOT_COMPLETED">
    </intent-filter>
</receiver>
```
3，发送广播
```
sendBroadcast(new Intent("特定得action");
```