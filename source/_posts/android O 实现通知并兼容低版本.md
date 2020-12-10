---
title: Android O 实现通知并兼容低版本
date: 2019-5-10 01:00:02
tags:
- Android
---
在activity中实现按钮弹出通知
* 首先activity的onCreate中首先绑定按钮
```
Button sendNotice =  (Button)findViewById(R.id.button_send_notice);
sendNotice.setOnClickListener(this);
```
<!-- more -->
* 再在onClick方法中
```
@Override
public void onClick(View view) {
    //判断sdk 当app版本为O或者以上时创建通知渠道
    if(Build.VERSION.SDK_INT >= 26)
        createChannel();
    switch (view.getId()){
        case R.id.button_send_notice:
            Intent intent = new Intent(this, NotificationActivity.class);
            PendingIntent pi = PendingIntent.getActivity(this, 0, intent, 0);
            Notification notification = new NotificationCompat.Builder(this, "channel_01")
                    .setContentTitle("This is content title")
                    .setContentText("Tis is content")
                    .setWhen(System.currentTimeMillis())
                    .setSmallIcon(R.mipmap.ic_launcher)
                    .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher))
                    .setContentIntent(pi)
                    .build();
            NotificationManager  manager = (NotificationManager)getSystemService(NOTIFICATION_SERVICE);
            manager.notify(1, notification);
            break;
        default:
            break;
    }
}
```

* 然后创建通知渠道的函数封装
```
@RequiresApi(api=26)
public void createChannel(){

    NotificationManager manager = (NotificationManager)getSystemService(Context.NOTIFICATION_SERVICE);
    //通知渠道组的id
    String group = "group_id";
    // 用户可见的通知渠道组名称.
    CharSequence group_name = getString(R.string.group_name);
    //创建通知渠道组
    manager.createNotificationChannelGroup(new NotificationChannelGroup(group, group_name));

    /**
     * 创建通知渠道1
     */

    //渠道id
    String id = "channel_01";
    //用户可以看到的通知渠道的名字
    CharSequence name = getString(R.string.channel_name);
    //用户可以看到的通知渠道的描述
    String description = getString(R.string.channel_description);
    int importance = NotificationManager.IMPORTANCE_HIGH;
    NotificationChannel channel = new NotificationChannel(id, name, importance);
    //配置通知渠道的属性
    channel.setDescription(description);
    //设置通知出现时的闪灯（如果android设备支持的话）
    channel.enableLights(true);
    channel.setLightColor(Color.GREEN);
    //设置通知出现时的震动（如果androd设备支持的话）
    channel.enableVibration(true);
    channel.setVibrationPattern(new long[]{100, 200, 300, 400, 500});
    //绑定通知渠道组
    channel.setGroup(group);
    //在notificationmanager中创建该通知渠道
    manager.createNotificationChannel(channel);
}
```