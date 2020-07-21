---
title: Build Your Own Git Server
date: 2019-08-08 16:59:19
tags: Git
categories: Programming
---

当我们在做团队项目时，如果需要进行代码管理而代码又不方便开源上传到 Github，则有两种方法可以满足**多人协作**以及**代码版本管理**的需求：
- 升级Github企业用户
- 搭建自己的代码托管服务器

下面主要介绍如何**搭建自己的代码托管服务器**。

# 远程服务器准备工作

## 创建 git 用户
创建一个新的 git 用户专门用于管理远程 git 代码仓库，可以防止 git 用户的配置文件与当前用户的配置文件发生冲突。
```bash
sudo adduser git # 创建 git 用户
su git           # 切换到 git 用户下     
```

## 创建远程仓库

在远程服务器中， 需要创建项目代码仓库文件夹，然后在该文件夹下初始化一个空的代码仓库:
```bash
mkdir -p ~/Repositories/Project.git  # 创建文件夹
cd ~/Repositories/Project.git  # 进入到仓库文件夹
git init --bare  # 初始化一个空仓库
```

# 免密码提交

在创建了远程仓库后，已经可以通过 `git clone ssh://server.ip:/path/to/*.git` 的命令来进行远程仓库的使用了，但是每次 `git push/pull/fetch` 等操作都会要求输入密码，十分的麻烦，因此下面我们通过生成 **ssh** 密钥来实现免密码连接远程仓库。

## 本地电脑生成密钥
使用 `ssh-keygen` 命令即可在本地电脑生成密钥，第一次询问是生成密钥的路径，第二次询问是生成密钥的密码。按照自己想要的方式设置好后，在 `~/.ssh` 目录下回生成 `id_rsa` 和 `id_rsa.pub` 两个文件，分别是私钥和公钥。

```bash
ssh-keygen -t rsa # 生成密钥
```

## 远程仓库授权公钥

在本地电脑中生成的公钥需要在远程仓库中授权才能使用免密码操作。

打开 `id_ras.pub` 文件，将里面的内容添加到远程服务器的 `~/.ssh/authorized_keys` 文件里面即可。

# 关联远程仓库

承接第一节的内容，在远程建立的一个空的仓库之后，如果想将本地的仓库关联到远程仓库并推送，可以通过以下代码实现。

```bash
git remote add origin ssh://git@server.ip:/path/to/*.git # 将本地仓库关联到远程仓库

git push -u origin master # 只有第一次远程推送需要加入 -u 参数
```

# 利用Gitlab搭建私有代码管理平台

Gitlab是一个开源的GIT仓库管理平台，主要面向中型代码团队，允许代码的成员在上面创建，修改代码库，并且追踪，修复bugs。搭建私有的Gitlab代码管理平台即相当于拥有一个部署在内网的Github，Gitlab网站，团队成员可以通过内网的网页访问代码。

## 安装必须的Ubuntu Packages

```bash
sudo apt-get update
sudo apt-get install -y curl openssh-server ca-certificates
```
## 安装邮件通知服务

Gitlab提供多种方式发送邮件通知，其中推荐是的**Postfix**和**SMTP**服务：

- 使用**Postfix**:
```bash
sudo apt-get install -y postfix
```
- 使用**SMTP**服务不需要安装额外的依赖库，只需要在部署好Gitlab后修改`/etc/gitlab/gitlab.rb`文件并执行`gitlab-ctl reconfigure`命令即可。通过[此链接](https://docs.gitlab.com/omnibus/settings/smtp.html)可以查看配置样例。
```bash
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.server"
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "smtp user"
gitlab_rails['smtp_password'] = "smtp password"
gitlab_rails['smtp_domain'] = "example.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_openssl_verify_mode'] = 'peer'

# If your SMTP server does not like the default 'From: gitlab@localhost' you
# can change the 'From' with this setting.
gitlab_rails['gitlab_email_from'] = 'gitlab@example.com'
gitlab_rails['gitlab_email_reply_to'] = 'noreply@example.com'
```

## 安装Gitlab

- 完成Gitlab依赖包的安装以后，即可通过添加Gitlab的下载链接来安装Gitlab：
```bash
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
```
- 安装Gitlab并修改访问地址：
```bash
sudo EXTERNAL_URL="http://gitlab.example.com" apt-get install gitlab-ce
```
安装完毕后，即可通过在浏览器中输入 [http://gitlab.example.com]() 来访问Gitlab代码部署平台。

## 配置Gitlab

如果想修改Gitlab相关的内容，可以通过修改其配置文件，修改后需要通过`gitlab-ctl reconfigure`命令来重新启动Gitlab
```bash
sudo vim /etc/gitlab/gitlab.rb
sudo gitlab-ctl reconfigure
```
{% note info %}
如果想要搭建公网的Gitlab代码管理平台，需要租借域名和购买证书，并进行额外的配置。
{% endnote %}


# Referencs

- [搭建属于你自己的 Git 服务器](https://zhuanlan.zhihu.com/p/40371444)
- [搭建自己的 Git 服务器](https://www.aneasystone.com/archives/2018/12/build-your-own-git-server.html)
- [Install self-managed Gitlab](https://about.gitlab.com/install/#ubuntu)
- [How to Install and Configure GitLab on Ubuntu 16.04](https://www.howtoforge.com/tutorial/how-to-install-and-configure-gitlab-on-ubuntu-16-04/)