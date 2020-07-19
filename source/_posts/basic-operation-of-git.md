---
title: Basic Operation of Git and Git Flow Strategy
date: 2019-06-28 17:04:50
tags: Git
categories: Programming
---

# Basic Operation of Git

## Fundamental Conception

**Git** 是一个很强大的代码版本管理软件，通过使用 **Git** 我们可以很方便地管理我们的代码，包括撤销操作，回退到某个版本，临时修改代码等。
**Git** 有四个基本空间，名为工作区(working directory)，暂存区(stage)，本地仓库(local repository)，
远程仓库(remote repository)。
- 工作区
  就是电脑里的代码目录，所有的操作都在此处进行。
- 暂存区
  也叫 index ，我们 `git add` 后的信息会被存放到此处，一般用于我们在开发过程中遇到另外的情况需要恢复
  到未修改的代码状态，但是又想保留已经更改的代码下次开发时，可以将修改的内容放到暂存区。
- 本地仓库
  **Git** 的版本库，通过本地仓库我们可以实现回退代码到某个版本的操作。
- 远程仓库
  服务器端的版本库，通过`git push`, `git pull`指令进行本地仓库和远程仓库的同步。

  <div align="center">
    <br><font size="4">Git Workflow</font> <br>
    <a href="https://imgur.com/GXW3rPh"><img src="https://i.imgur.com/GXW3rPh.png" title="source: imgur.com" /></a>
    <br><font color="gray">From http://files.zeroturnaround.com/pdf/zt_git_cheat_sheet.pdf</font>
</div>

## Common Commands

### 提交

日常最常用的指令就是修改代码后将修改提交到代码库。
```bash
git add .                 # 将所有修改加入到暂存库
git commit -m "comments"  # 将暂存库的内容提交到本地仓库
```

### 查看状态

了解当前工作环境的状态也是日常工作中的一部分。
```bash
git status           # 查看当前工作区的状态
git diff [filename]  # 查看某文件的不同
```

### 仓库同步

如果创建为本地仓库创建了远程仓库，在日常工作完毕后需要将工作推送到远程仓库。
```bash
git pull # 合并远程分支到本地
git push # 推送本地分支到远程
```

### 撤销&回滚

当因为某些原因想要撤销修改的时候，**Git** 同样可以方便地完成我们想要的操作。<br>

1. 只是修改了文件，没有`git add .`, 没有`git commit`
```bash
git checkout -- [filename]/.  # 可以选择单独撤销某个文件或本地所有文件
git clean -xdf                # 删除所有新文件
```

2. 已经`git add .`, 没有`git commit`
```bash
git reset HEAD  # 将暂存区的内容放回到工作区, 还需要继续 1 的操作
```

3. 已经`git commit`, 但是没有`git push`
```bash
git reset --hard <commit-id>  # 直接将代码重置到之前的某个版本
```

4. 已经`git push`，所以需要修改远程仓库
```bash
git revert <commit-id> -m
git push
```

### 储藏代码

当我们在开发代码的过程中，项目出现了意外情况（如 Bug 修复）需要我们用未修改过的代码进行项目开发的时候，我们又不想放弃当前修改的内容，便可以将修改内容先存放到暂存区。 然后再新的分支上进行 Bug 修复，最后再恢复暂存区的代码继续进行原有的开发。
```bash
git stash -u # 将修改的内容存放到暂存区，包括未跟踪的文件

git stash pop # 将暂存区的内容重新放入到工作区，继续开发
```

### 对比差异

在项目开发的过程中，有时候需要对比代码修改前后的差异，`git diff` 命令可以帮助我们完成这项工作。
```bash
git diff  # 默认为对比工作区和暂存区的所有文件的异同
git diff --cached  # 对比暂存区(git add . 以后)和最新本地版本库(上一次 commit)的区别
git diff HEAD  # 比较工作区和最新本地版本库的区别
git diff branch_name  # 比较工作区和指定分知名的差别
git diff <commit-id> <commit-id>  # 比较两次提交之间的差异
```

## Git Branch

**Git** 通过 *branch* 来实现版本管理的功能，通过在不同的分支上进行代码开发，只需要进行分支切换即可
实现使用不同版本的代码。

1. 查看分支
```bash
git branch -a  # 查看当前所有分支 
```

2. 创建分支
```bash
git checkout -b <branchName>  # 创建并切换到新的分支上
```

3. 切换分支
```bash
git checkout <branchName>  # 切换到已存在的分支上
```

4. 删除分支
```bash
git branch -d <branchName> # 删除本地仓库分支
git push origin --delete <branchName> # 删除远程仓库分支
```

5. 查看当前分支是否关联了远程分支
```bash
git branch -vv
```

6. 合并分支
```bash
git merge --no-ff <branchName>  # 将 branchName 分支合并到当前分支
git reset --hard <the number before merge> # 撤销合并（合并后没有push）
```

### 推荐的工作流

在日常代码迭代中，不提倡直接在原有分支上进行改动，而是切换到新的特性分支上进行代码迭代，经过测试后
再合并到原有分支上。

```bash
git checkout -b dev-newFeature dev   # 基于 dev 分支创建新的特性分支
git commit -a -m "comments"          # 修改代码并提交
git checkout dev                     # 切换到 dev 分支
git pull                             # 从远程仓库更新 dev 分支代码
git merge --no-ff dev-newFeature     # 合并特性分支到 dev
git push                             # 推送
git branch -d dev-newFeature         # 删除特性分支direction))
```

## Remote repository

本地仓库与远程仓库的关联，同步是十分重要的操作，根据不同的情况又有不同的操作，下面讲述在不同的情况下较好的操作方式。

### 创建本地仓库并关联远程仓库

假设在本地已经有现存的代码想加入到 **Git** 版本控制系统中，首先需要在本地建立仓库。
```bash
git init
git add .
git commmit -m "Repository initialization"
```

接下来需要在远程服务器或在 **Github** 上创建一个空仓库
```bash
git init --bare # Input this command in the remote server
```

为本地仓库和远程仓库添加关联
```bash
git remote add origin ssh://server.name@server.ip:/path/to/project.git # 为本地仓库添加远程仓库关联
git push -u origin master # 推送到远程仓库，第一次推送需要加上 -u
```

删除本地仓库和某远程仓库的关联
```bash
git remote remove origin
```

### 新建本地分支和远程分支并关联
假设想创建一个 `dev` 本地分支并创建其对应的远程分支，首先需要在本地创建新的分支
```bash
git checkout -b dev # 创建本地 dev 分支
```
然后创建一个新的远程 `dev` 分支
```bash
git push origin dev # 创建一个新的远程 dev 分支
```
为本地分支和远程分支添加关联
```bash
git branch --set-upstream-to origin/dev
```

### 基于远程分支创建新的本地分支
如果想要基于某个远程分支创建新的本地分支，首先需要拉取整个仓库到本地，然后查看该仓库拥有的远程分支，最后基于远程分支创建新的本地分支。
```bash
git fetch # 拉取远程仓库到本地
git branch -a # 查看本地分支及远程分支
git checkout -b dev origin/dev # 基于 origin/dev 分支创建本地 dev 分支
```

### 只克隆某一特定分支的 Git 仓库
如果只想克隆某一特定分支的 **Git** 仓库，可以使用以下命令
```bash
git clone -b [branch_name] server.name@repository.git
```

# Git Flow Strategy

持续代码交付方案中，有不同的代码分支策略，目前主流的代码分支策略中有两种，一是**主干开发**, 另外一种就是**Git Flow**。
**Git Flow** 模式下，有6个主要的分支，分别为：
- 主分支(master)
  稳定版本分支，对外可以随时编译发布的分支，不允许直接`push`代码，只能请求合并(`pull request`)，
  且只接受 hotfix, release 分支的代码合并
- 热修复分支(hotfix)
  针对现场紧急问题，bug 修复的分支，修复完后合并到主分支，开发分支  
- 发布分支(release)
  版本发布分支，用于迭代版本的发布。迭代完成后，合并 dev 代码到 release，在 release 分支上编译发布
  版本，以及修复 bug，测试完成后此版本可以作为发行版，然后把稳定的代码`push`到 master 上。
- 开发分支(dev)
  开发版分支，日常开发都应该在此分支上，迭代完成后合并到 release 分支。
- 特性开发分支(dev-feature)
  开发人员针对自己开发特性创建的基于 dev 的分支，开发完成后合并到 dev 分支上。

<div align="center">
    <br><font size="4">Git Branch Control</font> <br>
    <a href="https://imgur.com/UbwLdqs"><img src="https://i.imgur.com/UbwLdqs.png" title="source: imgur.com" /></a>
</div>

## 初始化配置

创建一个新的仓库时，可以按照下面的初始化各种分支。
```bash
git checkout -b hotfix                        # 创建 hotfix 分支
git push origin hotfix                        # 创建远程 hotfix 分支
git branch --set-upstream-to origin/hotfix    # 关联 hotfix 分支到远程

git checkout -b dev                           # 创建 dev 分支
git push origin dev                           # 创建远程 dev 分支
git branch --set-upstream-to origin/dev       # 关联 dev 分支到远程
   
git checkout -b release                       # 常见 release 分支
git push origin release                       # 创建远程 release 分支
git branch --set-upstream-to origin/release   # 关联 release 分支到远程
```

## 紧急Bug修复

当代码出现一个紧急 bug 的时候，可以按照以下的工作流程来进行 bug 修复。
```bash
git checkout -hotfix                   # 切换到 hotfix 分支
git pull gitlab master                 # 从远程 master 分支同步代码，同时更新到本地和远程的 hotfix 分支

git commit -a -m "comments"            # 提交代码
git push                               # 推送代码到远程主分支

git checkout dev                       # 切换到 dev 分支
git pull                               # 同步
git merge --no-ff hotfix               # 合并 hotfix 分支到 dev 分支
git commit -a -m "merge hotfix branch" # 提交代码
git push                               # 推送分支到远程

# 合并主分支
```

# References

- [持续交付之基于Git Flow代码分支策略实践](https://blog.csdn.net/zuozewei/article/details/93862974#_92)
- [Git 分支最佳实践](https://blog.csdn.net/fw0124/article/details/50426740)
- [git基于远程分支创建本地分支](https://www.jianshu.com/p/e1129ecdaed4)
- [Git创建本地分支并关联远程分支](https://blog.csdn.net/huangjw_806/article/details/78297851)


