---
title: adb 快捷命令脚本
date: 2019-11-19 10:21:00
tags:
- adb
- 脚本
---
   建议在sdk下adb文件同层目录下新建文件叫 adbs 把下面代码copy进去 
   使用时输入 adbs wifi 调起wifi应用
   不知道activity名字的情况下 可以尝试用这个打开应用
   adb shell monkey -p app.package.name -c android.intent.category.LAUNCHER 1
```
#!/bin/sh 
if [ $# == 0 ]; then
   echo "ps       -> adb shell ps | grep $2"
   echo "log 文件名    -> 拉取anr和logs日志"
   echo "wifi     -> Wifi"
	echo "mark      -> easimark"
	exit
fi

case $1 in 
 		   ps)
			   adb shell ps | grep $2
		    ;;
         log)
            DATE=$(date +%Y.%m.%d_%H.%m.%S)
            echo $DATE
            if [ ! "~/bug_log/$2$DATE/" ];then
            mkdir -p ~/bug_log/$2$DATE/
            fi
            adb pull /data/system/dropbox/ ~/bug_log/$2$DATE/
            adb pull /data/anr ~/bug_log/$2$DATE/
            adb pull /data/logs ~/bug_log/$2$DATE/
         ;;
         wifi)
         adb shell am start com.android.settings/com.android.settings.wifi.WifiSettings
         ;;
         mark)
         adb shell am startservice -W com.seewo.easimark/.MarkService
         ;;
         *)
         echo "No command"
         ;;
 esac 
```




