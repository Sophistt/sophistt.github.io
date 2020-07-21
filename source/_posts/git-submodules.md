---
title: Git Submodules
date: 2020-07-20 15:50:54
tags: Git
categories: Programming
---

# Description

Sometimes we have to include other Git repositories which are thrid-party repositories into our own Git repository. Git **sumodules** allow us to get it and track changes in several repositories via a centeral one. The extra repositories could be located anypwhere in a parent Git repository's workspace and are configured through `.gitsubmodules` file located at the root of the parent repository.

# Creating a submodule 

## Add a submodule 

- Clone a repository into the **Local Folder** as a submodule and initialize the submodule.
```bash
git add submodule add [URL to Git Repo] [Local Folder]
git submodule init
```

- Similarly, we could clone a specific branch of the repository:
```bash
git add submodule -b [Branch] [URL to Git Repo] [Local Folder]
git submodule init
```

After executing the above commands, the `.gitsubmodules` file that records the inforamtion about sub-repositories will created in the root of the parent repository.

## Checkout the status of submodules

Use `git submodule` command to checkout the status of submoules, if `-` exists before the hash, it means this submodule is not initialized yet.
```bash
git submodule

e33f854d3f51f5ebd771a68da05ad0371a3c0570 assets (heads/master)
-x5kyt51ogia51f5ebd771a68da05ad0371a3c0570 static
```

## Update submodules

Use `git submodule update (--remote)` to pulls in new commits into the main repository and its submodules.
```bash
git submodule update --remote
```

# Working with a repository with submodules

There two ways to clone a repository with submodules:

## Clone repository step by step

1. clone the parent repository
```bash
git clone [URL to Parent Git Repo]
```

2. Checkout the submodule status
```bash
git submodule
```

3. Initialize the submodule
```bash
git submodule init
```

4. Update the submodule
```bash
git submodule update 
```

## clone repository recursively
```bash
git clone --recursive [URL to Parent Git Repo]
```

If you already have cloned a repository and now want to load it’s submodules you have to use submodule update.
```bash
git submodule update --init --recursive
```

# Delete submodules

In order to delete submodules correctly, we need to delete the folders manually. 

1. Delete the folder of submodules
```bash
git rm --cached [submodule]
rm -rf [submodule]
```

2. Delete relevant configuration in `.gitsubmodules`
```bash
[submodule "[submodule]"]
    path = [submodule]
    url = [URL to Submodule Git Repo]
```
3. Delete relevant configuration in `.git/config`
```bash
[submodule "[submodule]"]
    url = [URL to Submodule Git Repo]
```

4. Delete files in `.git` folder
```bash
rm -rf .git/modules/[submodule]
```

# References

- [Using submodules in Git - Tutorial](https://www.vogella.com/tutorials/GitSubmodules/article.html)
- [Git submodule 子模块的管理和使用](https://www.jianshu.com/p/9000cd49822c)




