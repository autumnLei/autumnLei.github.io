---
title: log 打印一个值的堆栈信息
date: 2019-08-26 20:29:46
tags:
- Android
- log
---
int value;

Log.d("2019", " : " + value  + ", " + Log.getStackTraceString(RuntimeException()) + ",  " + (tableRecogHelper == null));