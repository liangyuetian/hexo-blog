---
title: git-常用命令
tags: Git
categories: Git
abbrlink: 265218565
date: 2019-12-30 10:56:56
---

## 查看仓库大小

```bash
du -sh
```

## 向仓库强制推送所有的变化：

```bash
git push origin master --force
```

<!-- more -->

## 删除不存在对应远程分支的本地分支

使用 <code>git remote prune origin</code> 可以将其从本地版本库中去除。

## 重命名本地分支 -m

```bash
git branch -m devel develop
```

## 完成撤销,同时将代码恢复到前一 commit_id 对应的版本。

```bash
git reset --hard commit_id
```

## 完成 Commit 命令的撤销，但是不对代码修改进行撤销，可以直接通过 git commit 重新提交对本地代码的修改。

```bash
git reset commit_id
```

## 只获取最新的 commit 记录

```bash
git clone https://github.com/SuperAL/splice.git --depth=1
```

# `--depth=1`，代表只拉取最新一次提交。

## 查看 remote 地址，远程分支，还有本地分支与之相对应关系等信息

```bash
git remote show origin
```

## 查看远程仓库已经不存在的分支

```bash
git remote prune origin
```

## 拉取远程到本地，并更新本地和远程的对应关系，同步时顺便删除远程已删除的分支

```bash
git fetch -p
```

## 删除本地分支（远程已经没有的对应的分支）

```bash
git branch -r | awk '{print $1}' | egrep -v -f /dev/fd/0 <(git branch -vv | grep origin) | awk '{print $1}' | xargs git branch -d
```

## 看下自己最近的一些删除的提交

```bash
git fsck --lost-found
```

## merge 回退

```bash
git merge --abort
```

## 删除本地远程不存在的分支

```bash
git remote prune origin
```

git fetch -p

## 删除远程分支

```bash
git push origin --delete <BranchName>
```

## git clone 时输错密码

windows 系统执行命令： <code>git credential-manager uninstall</code>
mac 系统命令为： <code>git credential-osxkeychain uninstall</code>

## 重新设置本机 git 配置

```bash
git config --global credential.helper store
```

## git 配置的 http 或 https 代理

```bash
git config --global http.proxy 127.0.0.1:51441 # 设置代理
git config --local http.proxy 127.0.0.1:51441 # 设置本仓库代理

git config --global --unset http.proxy # 删除全局代理
git config --local --unset http.proxy # 删除本地仓库代理
```

## git 查看分支并格式化时间

```bash
git for-each-ref --sort=committerdate refs/heads/ --format='%(committerdate:short) %(refname:short)'
```

## 清除仓库中的文件

```bash
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch *.class'   --prune-empty --tag-name-filter cat HEAD -- --all
```

## 忽略某些文件

```bash
git update-index --assume-unchanged ./config/index.js
```

## git 无历史合并

```bash
git merge master --allow-unrelated-histories
```
