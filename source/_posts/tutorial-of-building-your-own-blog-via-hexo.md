---
title: Tutorial of Building Your Own Blog via Hexo
date: 2020-07-18 01:16:32
tags: Hexo
categories: Frontend
mathjax: true
---

# Install dependencies

## Install Nodejs and npm

In order to install **hexo**, **Node.js** and **npm** are required to be installed first. 

- Ubuntu (install Node.js LTS - v12.x): 
```bash
curl -sL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs
```

- Windows:
Download and install **Node.js** from its [offical website](https://nodejs.org/zh-cn/).
{% note info %}
**npm** will be installed automaticallly while **Node.js** being installed.
{% endnote %}

## Install Hexo

**Hexo** is a fast, simple & powerful blog framework, powered by **Node.js**. **Hexo** can be installed easily by **npm**:
```bash
npm install hexo-cli -g 
```

# Build the blog

## Create a workspace

Workspace is an important folder where all your blog files are saved.
```bash
mkdir personal_web
cd personal_web
```

## Initialzie the blog

- New blog
```bash
hexo init
npm install
```

- Existing blog
```bash
git clone https://path.to.your.git
npm install
```

## Local debugging
```bash
hexo clean # clean the workspace, delete database and the public folder
hexo g(generate)
hexo s(serve)
```
After executing the command `hexo s`, we could open the local website through the browser by entering [lcoalhost:4000](localhost:4000).


# Tricks

The server and repository of **npm** are overseas, hence some unforeseen problems may occur while executing the command `npm install` because of unstable Internet connection. In order to resolve the problem, we could reset the proxy of **npm** or use domestic registry.

- reset the proxy
```bash
npm config set proxy 127.0.0.1:port
npm config set https-proxy 127.0.0.1:port
```

- use domestic registry
```bash
npm config set registry https://registry.npm.taobao.org
```

# References

- [nodesource](https://github.com/nodesource/distributions)
- [npm install安装到一半卡住的解决方案](https://juejin.im/post/5d8eeb2de51d4578200cc968)




