---
title: linux文件权限操作
date: 2019-12-30 16:31:59
tags:
- linux
- chmod
- 权限
---

** 新建文件是没有可读写执行权限的灰色 **
** linux权限区分大小写 **

- u 表示该文件的拥有者，g 表示与该文件的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示这三者皆是。
	把file.txt设置为所有人可读的两种写法
	```
	chmod ugo+r file.txt
	chmod a+r file.txt
	```

- r 表示可读取，w 表示可写入，x 表示可执行，X 只有在文件是目录或对一些用户已经有执行权限的时候执行/搜索？
	新建test.sh想执行时
	```
	chmod +x test.sh
	```

- 加号+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
	想取消给test.sh的可执行权限时
	```
	chmod -x test.sh
	```
	想给file文件夹和里面所有东西可写操作时
	```
	chmod -R +w file
	```

- 数字型写法
	给file文件夹和里面所有文件所有权限
	```
	chmod -R 777 file
	```


