---
title: Linux 数据库基本命令行操作
tags:
- 数据库
- Linux
---
登录数据库: mysql -u用户名字 -p密码
<!-- more -->
//不要漏了“；”
显示数据库: show databases; 

创建数据库: create database 数据库名字;

选择数据库: use 数据库名字 ;

直接删除数据库不提醒: drop database 数据库名字;

显示表选中数据库的所有表名: show tables;

显示选中数据库的某个表具体结构: describe 表名;

导入sql文件: mysql>source /var/www/html/xxx.sql;