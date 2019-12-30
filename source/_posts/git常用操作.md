---
title: git常用操作
date: 2019-09-04 11:07:36
tags:
- git
---

#### commit提交
```
// 查看commit历史信息
git log
// 查看 commit了的文件 修改了还没commit的文件  没有add的文件
git status
// 打tag 先提交commit 
git tag -a 1.0.0 -m "备注 可不加"
- os组的要在tag里标注好渠道如 "commit的备注" [publish] platform=811 customer=客户名称 flavor=渠道名称
git push  
git push --tags  也可在push后指定分支   
<font color=red>一般一个tag触发一次编译 补东西时也提下tag</font>
```

#### 本地撤销commit
```
// 回退一个commit同时撤销修改的代码
git reset --hard HEAD~
// 回退两个commit
git reset --hard HEAD~~
 - 如果这时候想跳到远程最新commit 直接 git pull
// 回退一个commit 代码不变
git reset --soft HEAD~
// 回退一个commit 代码不变 当前面两个reset都回不到你想要的commit时用这个
git reset --hard commit号
// 新建commit抵消指定commit 
git revert commit号
// push后 reset后stash再强制推送本地版本 会清空所有其他人后面的提交
git push origin 分支 --force
```

#### 本地暂存代码
```
// 保存当前进度
git stash
// 恢复最新进度到工作区
git stash pop
```

#### 建立 删除 切换分支
```
// 新建分支
git branch 分支名
// 切换分支
git checkout 分支名
// 推送分支到远程
git push origin 分支名
// 删除远程分支
git push origin --delete 分支名
// 删除本地分支：
git branch -d 分支名（remotes/origin/分支名）
// 强制删本地：
git branch -D 分支名
```

