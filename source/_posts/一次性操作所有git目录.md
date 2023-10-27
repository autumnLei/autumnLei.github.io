---
title: 一次性操作所有git目录
date: 2019-09-02 14:56:31
tags:
- git
- android
---

### 首先cd到根目录

### 一次性pull所有git目录代码
```
find ./ -name ".git" -exec git -C {}/../ pull \; 
```

### 一次性切换所有git目录分支到develop
```
find ./ -name ".git" -exec git -C {}/../ checkout develop \;
```
