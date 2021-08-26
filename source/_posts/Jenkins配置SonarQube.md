---
title: Jenkins配置SonarQube
date: 2020-12-15 15:25:19
tags:
- Jenkins
- SonarQube
---

## 项目

#### 修改项目根目录build.gradle
```
buildscript {
  repositories {
    maven {
      url "https://plugins.gradle.org/m2/"
    }
  }
  dependencies {
    classpath "org.sonarsource.scanner.gradle:sonarqube-gradle-plugin:3.0"
  }
}

apply plugin: "org.sonarqube"
```
Kotlin写法见 plugins.gradle.org/plugin/org.sonarqube

## SonarQube

#### 新建网络调用
首页顶部 配置->配置->创建
URL填Jenkins地址加sonarqube-webhook/后缀
如 http://10.120.11.140:8080/sonarqube-webhook/

#### 在SonarQube首页右上角 手工创建一个项目 标识和令牌名可以填流水线名字
复制令牌 如 8b6ce1ef84dd9d0f858e8849ba5165eb169e6539 
然后选择编译方式

## Jenkins

#### 安装sonarQube插件
jenkins->插件管理->SonarQube Scanner for Jenkins

#### Jenkins单个项目的SonarQube关联 方式一
jenkins>Manage Jenkins>global configuration
Name填 对应SonarQube项目的名字 如sonar
->添加
类型选择 Secret text
Secret填 对应SonarQube项目建立时的密钥 8b6ce1ef84dd9d0f858e8849ba5165eb169e6539

然后修改jenkinsfile
```
withSonarQubeEnv('sonar') {
	./gradlew --no-daemon app:assembleRelease
}
```

#### Jenkins单个项目的SonarQube关联 方式二
复制项目命令到jenkinsfile里 例：
```
./gradlew sonarqube \
  -Dsonar.projectKey=App_Manual_PipleLine \
  -Dsonar.host.url=http://10.120.11.140:9000 \
  -Dsonar.login=8b6ce1ef84dd9d0f858e8849ba5165eb169e6539
```

























