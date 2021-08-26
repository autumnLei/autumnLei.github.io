---
title: 保存本地安卓项目到github
date: 2020-3-10 01:00:50
tags:
- github
- git
---

#### 在本地项目目根目录执行
```
git init
```

#### 修改项目中的.gitignore文件为
```
# IntelliJ IDEA
.idea
*.iml
 
# Gradle
.gradle
build
 
# Local configuration file (sdk path, etc)
local.properties
 
# Mac system files
.DS_Store

# 如项目中用到了NDK,就可以加上/obj
```
#### 在github创建同名项目 写上项目名点创建

#### 再在本地目录下执行
```
git add .
git commit -m "代码备份."
git remote add origin git@github.com:autumnLei/DatabindingDemo.git
git push -u origin master
```

#### 问题
push时不能覆盖远程已有提交 git push -uf origin master
origin别名已经存在 git remote rm origin






