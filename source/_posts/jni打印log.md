---
title: jni打印log
date: 2019-07-31 21:30:08
tags:
- Android
- jni
---
int len = 1;
__android_log_print(ANDROID_LOG_ERROR, "PinyinIME", "leb before: %d", len);