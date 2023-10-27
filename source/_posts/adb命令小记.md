---
title: adb命令小记
date: 2019-07-25 15:15:01
tags:
- Android
- adb
---

# adb命令小记
#### 1、打开adb调试？如何打开开发者模式？如何进入工厂菜单？进入工厂菜单的方式有什么途径？
	遥控器 debugmenu 进入Android set 全部打开
	tcl：setting->系统信息->遥控器按方向键上下左右->ADB开启
#### 3、连接整机进行调试
	adb connect ip:5555 旋转屏的是1379
#### 4、截图
	adb shell screencap -p /sdcard/sdkf.jpg
#### 5、将整机系统的一个文件拉取到电脑
	adb pull /sdcard/sdkf.jpg /Users/lei/downloads
#### 6、将电脑的一个文件推送到整机系统
	adb push /Users/lei/Downloads/s1.png /sdcard
#### 7、查看当前界面是哪个应用
	adb shell dumpsys window | grep mCurrentFocus
#### 8、打开某个Activity
	 Activity Manager(am)工具  shell 发送远程内核命令
	 adb shell am start -n com.example.myapplication/.main.MainActivity 
#### 9、发送某个广播
	同8 startservice
#### 10、打开某个服务
	[--user user_id | all | current]：指定要发送到的用户；如果未指定，则发送到所有用户
	同8 broadcast
#### 11、获取某个应用的版本信息
	adb shell dumpsys package com.example.myapplication | grep version
	#### 12、使用什么adb命令安装应用
	install [options] path	将软件包（通过 path 指定）安装到系统。
	选项：
	-l：安装具有转发锁定功能的软件包。
	-r：重新安装现有应用，保留其数据。
	-t：允许安装测试 APK。
	-i installer_package_name：指定安装程序软件包名称。
	-s：在共享的大容量存储（如 sdcard）上安装软件包。
	-f：在内部系统内存上安装软件包。
	-d：允许版本代码降级。
	-g：授予应用清单中列出的所有权限。
	adb install -r apk路径
#### 13、卸载应用
	-k：移除软件包后保留数据和缓存目录。
	adb uninstall com.example.myapplication
#### 14、获取某个系统属性值
	adb shell getprop [key]
	adb shell getprop 不加全显示
#### 15、设置某个系统属性值
adb shell setprop [key] [value]
#### 16、获得系统的ANR文件和log文件
	adb root
	adb pull /data/anr /Users/lei/Downloads
#### 17、查看系统CPU占用率
	adb shell top	
#### 18、查看某个应用的进程号？如何杀死某个应用？
	adb root
	adb shell ps 进程列表
	adb shell kill [pid]
#### 20 卸载系统应用 
	adb root
	adb remount
	adb shell
	cd /system/app/
	rm -rf PinyinIME/
#### 21 重启adb
	killall-adb 直接关掉 一般这样就行了

	adb kill-server 再任意命令启动adb
	或者
	mac下直接打开活动监视器 搜索adb强制关闭
#### 22 对某一模拟器执行命令：
	adb [-d|-e|-s ]
	-d 指定当前唯一连接的硬件设备为命令目标 -- 唯一连接的 USB 设备
	-e 指定当前唯一运行的模拟器设备为命令目标 -- 唯一运行的模拟器
	abd -s 要指定的模拟器编号 命令
#### 23 查看当前系统内存限制
	java虚拟机的最大内存限制
	adb shell getprop dalvik.vm.heapgrowthlimit
	单个程序限制最大内存
	adb shell getprop dalvik.vm.heapsize
#### 23 列出adb连接设备所有应用
	adb shell pm list packages
#### 24 强制退出某个应用
	adb shell am force-stop com.seewo.easinote
#### 25 输出日志到本地
	adb logcat > /Users/lei/Downloads/logcat.txt
#### 26 带参启动应用
	adb shell am start -n com.a.AApp/.UI.AActivity --ei ACTIONTYPE 66 --es URL "string" --es LOCATION "string"
#### 27 显示文件大小
	adb shell du -mh text.file

