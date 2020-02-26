---
title: git找回代码
date: 2019-10-31 15:59:41
tags:
- git
---

## 找回git checkout丢失的代码
git reflog
git reset –hard HEAD@{1} 1或者其他最近的数字


## git找回drop了的stash
数据类型	含义
blobs	每个blob代表一个（版本的）文件，blob只包含文件的数据，而忽略文件的其他元数据，如名字、路径、格式等。
trees	每个tree代表了一个目录的信息，包含了此目录下的blobs，子目录（对应于子trees），文件名、路径等元数据。因此，对于有子目录的目录，git相当于存储了嵌套的trees。
commits	每个commit记录了提交一个更新的所有元数据，如指向的tree，父commit，作者、提交者、提交日期、提交日志等。每次提交都指向一个tree对象，记录了当次提交时的目录信息。一个commit可以有多个（至少一个）父commits。
tags	tag用于给某个上述类型的对象指配一个便于开发者记忆的名字, 通常用于某次commit。

### 查找所有unreachable记录
	git fsck --unreachable

### git show hash码
	主要针对commit类型查看 - 可用 git cat-file commit hash码

### git stash apply hash码
	一般也是针对commit类型的
	也可先git branch 分支名 hash码 再git merge 分支名

https://stackoverflow.com/questions/89332/how-to-recover-a-dropped-stash-in-git