---
layout: post
title:  "Git Cheat Sheet Chinese"
date:   2014-09-26 10:00:00
categories: tool
tags: git
author: "Victor"
---

## 创建


```bash
# 复制一个已创建的仓库:
$ git clone ssh://user@domain.com/repo.git

# 创建一个新的本地仓库:
$ git init
```

## 本地修改

```bash
# 显示工作路径下已修改的文件：
$ git status

# 显示与上次提交版本文件的不同：
$ git diff

# 把当前所有修改添加到下次提交中：
$ git add .

# 把对某个文件的修改添加到下次提交中：
$ git add -p <file>

# 提交本地的所有修改：
$ git commit -a

# 提交之前已标记的变化：
$ git commit

# 附加消息提交：
$ git commit -m 'message here'

# 修改上次提交
# Don't amend published commits!
$ git commit --amend
```

## 提交历史

```bash
# 从最新提交开始，显示所有的提交记录（显示hash， 作者信息，提交的标题和时间）：
$ git log

# 查看最后一次提交及其改动：
$ git log 1 -p

# 一行显示一个提交：
$ git log --pretty=oneline

# 以树状图显示提交历史：
$ git log --graph --oneline --decorate --all

# 显示所有提交（仅显示提交的hash和message）：
$ git log --oneline

# 显示某个用户的所有提交：
$ git log --author="username"

# 显示某个文件的所有修改：
$ git log -p <file>

# 谁，在什么时间，修改了文件的什么内容：
$ git blame <file>
```

## 分支与标签

```bash
# 列出所有的分支：
$ git branch

# 切换分支：
$ git checkout <branch>

# 基于当前分支创建新分支：
$ git branch <new-branch>

# 基于远程分支创建新的可追溯的分支：
$ git branch --track <new-branch> <remote-branch>

# 删除本地分支:
$ git branch -d <branch>

# 给当前版本打标签：
$ git tag <tag-name>
```

##更新与发布

```bash
# 列出对当前远程端的操作：
$ git remote -v

# 显示远程端的信息：
$ git remote show <remote>

# 添加新的远程端：
$ git remote add <remote> <url>

# 下载远程端版本，但不合并到HEAD中：
$ git fetch <remote>

# 下载远程端版本，并自动与HEAD版本合并：
$ git remote pull <remote> <url>

# 将远程端版本合并到本地版本中：
$ git pull origin master

# 将本地版本发布到远程端：
$ git push origin <remote> <branch>

# 删除远程端分支：
git push <remote> --delete <branch> (since Git v1.7.0)

# 发布标签:
$ git push --tags
```

## 合并与重置

```bash
# 将分支合并到当前HEAD中：
$ git merge <branch>

# 将当前HEAD版本重置到分支中:
# Don't rebase published commit!
$ git rebase <branch>

# 退出重置:
$ git rebase --abort

# 解决冲突后继续重置：
$ git rebase --continue

# 使用配置好的merge tool 解决冲突：
$ git mergetool

# 在编辑器中手动解决冲突后，标记文件为`已解决冲突`
$ git add <resolved-file>
$ git rm <resolved-file>
```

## 撤销

```bash
# 放弃工作目录下的所有修改：
$ git reset --hard HEAD

# 移除缓存区的所有文件（i.e. 撤销上次`git add`）:
$ git reset HEAD

# 放弃某个文件的所有本地修改：
$ git checkout HEAD <file>

# 重置一个提交（通过创建一个截然不同的新提交）
$ git revert <commit>

# 将HEAD重置到上一次提交的版本，并放弃之后的所有修改：
$ git reset --hard <commit>

# 将HEAD重置到上一次提交的版本，并将之后的修改标记为未添加到缓存区的修改：
$ git reset <commit>

# 将HEAD重置到上一次提交的版本，并保留未提交的本地修改：
$ git reset --keep <commit>

# 将本地仓库重置成与远端一样：
$ git fetch origin git reset --hard origin/master
```

## 暂存

```bash
$ git stash
$ git stash list
$ git stash pop
```

## 原文链接

* [Git Cheat Sheet](https://github.com/flyhigher139/Git-Cheat-Sheet)
* [git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)
