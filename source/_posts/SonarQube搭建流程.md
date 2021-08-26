---
title: SonarQube搭建流程
date: 2020-12-10 19:57:15
tags:
- Jenkins
- SonarQube
---

##### -linux版

#### 官网下载地址
https://www.sonarqube.org/success-download-community-edition/

#### 解压到指定目录 如 /usr/local/
将 文件/文件夹 从本地拷至远程服务器
```
scp -r localfile.txt username@192.168.0.1:/usr/local/
```
解压tar.gz
```
tar -zxvf filename.tar.gz
```

#### 安装java11 
如已安装需要修改/etc/profile 然后source /etc/profile
如果修改后没有生效 
```
rm -rf /usr/bin/java
rm -rf /usr/bin/javac
ln -s $JAVA_HOME/bin/javac /usr/bin/javac
ln -s $JAVA_HOME/bin/javac /usr/bin/java
```
这样执行以后，然后 java -version 、 javac -version 

#### ssh连接服务器
需要非root账号连接
##### 创建新用户sonar
```
adduser sonar
```
##### 为sonar创建密码
```
passwd sonar
```
##### 修改sonarqube的目录和用户组为sonar
```
chown -R sonar:sonar /usr/local/sonarqube-8.5.1.38104
```

#### 启动SonarQube 
```
sh /usr/local/sonarqube-8.5.1.38104/bin/linux-x86-64/sonar.sh start
```
sonar.sh status可以查看启动状态
日志在/usr/local/sonarqube-8.5.1.38104/logs/
默认地址http://localhost:9000
如果在服务器本地可以curl 但是访问不了服务器ip:9000 可能是端口没打开 使用root ssh账号登陆然后
```
/sbin/iptables -I INPUT -p tcp --dport 9000 -j ACCEPT
```

#### 登陆SonarQube 
账号 密码都是 admin

#### 安装汉化插件
Administration->Marketplace搜Chinese









