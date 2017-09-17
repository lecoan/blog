---
title: Git使用手册
date: 2017-09-17 19:34:07
tags: Git
---

# Git使用手册

新建git仓库

```shell
git init
```

显示当前分支

```shell
git branch
```

<!--more-->

切换分支

```shell
git branch <brach-name>
```

切换分支（若不存在则新建）

```shell
git branch -b <brach-name>
```

合并分支

```shell
git merge <branch-name>
```

从远端仓库clone

```shell
git clone <your-url>
```

只克隆最新版本

```shell
git clone <your-url> --depth 1
```

将本地分支推送到远端

```shell
git push
```

将本地分支推送到远端的特定分支

```shell
git push origin <branch-name>
```

从远端同步

```shell
git pull
```

关联本地分支和远端分支

```shell
git branch --set-upstream-to=origin/<branch-name>
```

