---
title: 解析gradle依赖树的python脚本
date: 2021-05-26 10:50:56
tags:
- gradle
- python
- 依赖树
---

### gradle打印依赖的两种方式
```
# 以树形结构打印依赖
./gradlew app:dependencies
```
```
# 打印拉平后的依赖
./gradlew androidDependencies
```
[gradle官方文档](https://docs.gradle.org/current/userguide/viewing_debugging_dependencies.html)

### 解析树形结构依赖
```
#!/usr/bin/env python

import re
import sys
import json
import requests

stack = []
data = []


def format(str, index):
    str = re.sub(r"{.*} -> ", "", str)
    temp = str.replace("\n", "").replace(" (*)", "").replace(" (c)", "")
    if "-> androidx" in str and index == 1:
        return temp.split(" ->")[1]
    elif " ->" in str and index == 1:
        return re.sub(":[0-9].* -> ", ":", temp)
    else:
        return temp.split(" ->")[0]


def loop(stack):
    branch = ""
    projectUrl = ""
    dependency = ""
    if len(stack[-1].split("--- ")[0]) == 1:  # 一级依赖
        componentName = format(stack.pop().split("--- ")[1], 1)
        branch = sys.argv[1]
        projectUrl = sys.argv[2]
    else:
        while len(stack) > 1 and len(stack[-1].split("--- ")[0]) == len(stack[-2].split("--- ")[0]):
            dependency += format(stack.pop().split("--- ")[1], 0) + ","
        dependency += format(stack.pop().split("--- ")[1], 0)
        componentName = format(stack[-1].split("--- ")[1], 1)
        if len(stack[-1].split("--- ")[0]) == 1 or (
                len(stack) > 1 and "project :" in stack[-2]):  # 一级依赖
            branch = sys.argv[1]
            projectUrl = sys.argv[2]
    if "project :" not in componentName and "project :" not in dependency:
        data.append({
            "branch": "" + branch + "",
            "componentName": "" + componentName + "",
            "dependency": "" + dependency + "",
            "nagaReport": "",
            "projectUrl": "" + projectUrl + ""
        })
    return


for line in open("dependency.log"):
    if re.search("--- ", line):
        # 不统计无效依赖
        if " FAILED" in line or "(n)" in line:
            continue
        while stack and len(stack[-1].split("--- ")[0]) > len(line.split("--- ")[0]) or (
                line.split("--- ")[0]) == 1:
            loop(stack)
        stack.append(line)

# 去重
temp = []
[temp.append(i) for i in data if not i in temp]

# 上传到ape
r = requests.post(
    'http://10.120.11.210:9090/ape/componentWhiteList/saveOrUpdateAppDirectDependency', json=temp)
print(r.text)

# 本地保存份上传的数据
fo = open("dependency.json", "w")
fo.write(json.dumps(temp))
```

[requests库官方文档](https://docs.python-requests.org/zh_CN/latest/user/quickstart.html)