---
title: Use Vimdiff as Git Mergetool
date: 2019-08-21  16:56:45
tags: Git
categories: Programming
---

# 什么是 vimdiff ？
**vimdiff** 是 vim 文本编辑器自带的用于对比文件差异的工具，详细说明 [点击此处](https://sophistt.github.io/linux/2019/08/20/vimdiff.html)。
因此，**vimdiff** 可以用于解决 git 合并的产生的冲突。

# 使用方法

## Git 配置
使用 **vimdiff** 之前需要对 `git mergetool` 进行配置，指定 **vimdiff** 作为其默认的查看工具。
```bash
git config merge.tool vimdiff  # 指定vimdiff作为默认mergetool
git config merge.conflictsytle diff3  # 设定mergetool风格
git config mergetool.prompt false  # 取消打开文件时的warning显示
```
## 使用流程
当我们在使用`git pull`或者`git merge`命令因为文件冲突报错而不能成功合并时，通过执行`git mergetool`命令打开 vimdiff.

vimdiff 打开后，可以看到4个窗口排列成两行：

第一行的3个窗口从左到右分别被命名为`LOCAL`,`BASE`,`REMOTE`，分别代表了当前分支上文件的内容，两个分支的上游分支的内容，远程另一分支的内容。

第二行的窗口命名为`MERGED`，显示了合并结果，通过修改`MERGED`窗口的内容得到最终想要保留的结果后即可保存提交。

### 命令
移动到`MERGED`窗口后，将光标移动到冲突处
```bash
:diffget LOCAL  # 冲突内容采用LOCAL分支的内容
:diffget BASE   # 冲突内容采用BASE分支的内容
:diffget REMOTE # 冲突内容采用REMOTE分支的内容
```

修改完毕后，使用`:wa`命令保存修改，然后使用`:qa`退出编辑器，执行`git commit`即可提交修改。


# References

- [使用vimdiff作为git mergetool](https://kinboyw.github.io/2018/10/09/Use-Vimdiff-As-Git-Mergetool/)
