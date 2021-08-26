---
title: gradle常用命令
date: 2021-07-19 15:38:43
tags:
- gardle
---

### 常用后缀
```gradle
// 设置日志的输出样式
--console=(auto,plain,rich,verbose)
没adb连接设备时：
auto
	BUILD SUCCESSFUL in 961ms
rich
	BUILD SUCCESSFUL in 961ms
plain
	> Task :app:preBuild UP-TO-DATE
	> Task :app:preDebugBuild UP-TO-DATE
	> Task :app:compileDebugRenderscript NO-SOURCE
	> Task :app:generateDebugResValues UP-TO-DATE
	> Task :app:generateDebugResources UP-TO-DATE
	...
	BUILD SUCCESSFUL in 961ms
verbose // UP-TO-DATE是彩色的
	> Task :app:preBuild UP-TO-DATE
	> Task :app:preDebugBuild UP-TO-DATE
	> Task :app:compileDebugRenderscript NO-SOURCE
	> Task :app:generateDebugResValues UP-TO-DATE
	...
	BUILD SUCCESSFUL in 961ms

// 离线构建 不更新依赖
--offline 
// 强制更新依赖
--refresh-dependencies
// 使用Gradle守护程序构建
--daemon, --no-daemon
// 在gradle.com生成一份分析报告
--scan
```

### 编译bundle
```
 ./gradlew app:bundleDebug
```
### 显示所有task
```
 ./gradlew tasks --all
```






