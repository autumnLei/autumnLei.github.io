---
title: jenkinsfile常用语句
date: 2020-11-15 09:09:58
tags:
- Jenkins
- shell
- groovy
---
1、打印时间
```
echo $(date "+%Y-%m-%d %H:%M:%S")
```
2、cd回上个路径
``` 
cd -
```
3、改变全局变量
```
// 修改全局build number变量
env.BUILD_NUMBER = "hi"
```
4、显示当前空间前20大目录
```
du --max-depth=1 -h -BG ./ | sort -nr | head -n 20
```
<!-- more -->