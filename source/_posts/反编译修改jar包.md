---
title: 反编译修改jar包
date: 2021-08-27 01:34:23
tags:
- 反编译	
---

### 只需要修改资源文件
以[fireline](http://magic.360.cn/zh/index.html)为例

##### 解压jar包
```shell
# -q：执行时不显示任何信息；
# -o：不必先询问用户，unzip执行后覆盖原有的文件；
# -d<目录>：指定文件解压缩后所要存储的目录；
unzip -qo fireline_1.7.3.jar -d fireline_1.7.3
```

##### 修改资源文件
```shell
# -f：若目标文件或目录与现有的文件或目录重复，则直接覆盖现有的文件或目录；
mv -f ./RedLineRule_All_zh_CN.xml ./fireline_1.7.3/xml/RedLineRule_All_zh_CN.xml
```

##### 重新打包
```shell
# -c --create 创建存档。
# -v --verbose 将详细输出发送或打印到标准输出。
# -f=文件 --file=文件 指定存档文件名。
# -m=文件 --manifest=文件 包括来自给定清单文件的清单信息。
# -C 目录 更改指定的目录并包括在命令行末尾指定的文件。
jar cvfm fireline_1.7.3.jar ./fireline_1.7.3/META-INF/MANIFEST.MF -C ./fireline_1.7.3/ .
```

### 需要修改java文件 
需要的工具：[JD-GUI](http://java-decompiler.github.io/)

##### 使用JD-GUI解压jar包 然后File->Save All Souces

##### 修改需要修改的java文件

##### 编译修改过的java文件
如果编译的时候有缺失的依赖可以添加到-classpath中
如果缺失的依赖和代码没什么用可以直接删了
```shell
# --class-path 路径、-classpath 路径或-cp 路径
# 指定在哪里可以找到用户类文件和注释处理器。该类路径覆盖CLASSPATH环境变量中的用户类路径。

# 如果--class-path，-classpath或者-cp未指定的，那么用户类路径是价值CLASSPATH的环境变量，如果设置，否则当前目录。

# 如果不是为模块编译代码，如果--source-path没有指定 or -sourcepath` 选项，那么还会在用户类路径中搜索源文件。

# 如果-processorpath未指定该选项，则还会在类路径中搜索注释处理器。

javac -cp analyzer.jar:android.jar DependenciesCheckTask.java
```

