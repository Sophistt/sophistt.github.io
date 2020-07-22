---
title: Hexo 中 Mathjax 渲染出错问题解决
date: 2020-07-22 09:50:53
tags: Hexo
categories: Frontend
---


# 问题

Hexo 在使用 Mathjax 渲染数学公式的时候，由于 Markdown 语法与 Mathjax 语法冲突的问题，可能会导致使用到`_`, `\` 符号的公式渲染出现错误。

# 解决方法

有多种解决该问题的方法，但是不同的方法都有各自的优缺点，根据自己的需要选择合适的方法即可。

1. 修改源码

   优点：部署简单，渲染的兼容性最好
   
   缺点：修改源码可能引入额外的bug，不利于自动化部署

2. 使用`pandoc`渲染引擎

   优点：操作简单，可用于自动化部署

   缺点：需要安装`pandoc`, 引入额外的开销，博客的部分格式需要注意

## 修改源码

### 更换`kramed`渲染引擎
`hexo-renderer-kramed`渲染引擎是基于`hexo-renderer-marked`引擎修改的，该引擎修复了一些`marked`引擎存在的bug，但是仍然无法完美的渲染数学公式。
```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

### 修改`kramed`引擎代码

到博客根目录下，找到`node_modules/kramed/lib/rules/inline.js`文件，把第11行的`escape`值做出以下修改：
```js
//  escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
  escape: /^\\([`*\[\]()#$+\-.!_>])/,
```
在把第20行的`em`值做出相应的修改：
```js
//  em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
  em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

### 在配置文件中配置插件

在根目录下找到`_config.yml`文件，粘贴下列内容到里面：
```yaml
## Markdown Katex
kramed:
  gfm: true
  pedantic: false
  sanitize: false
  tables: true
  breaks: true
  smartLists: true
  smartypants: true
```

## 更换`pandoc`作为渲染引擎

### 安装`pandoc`

不同的操作系统，在`pandoc`[官网](https://pandoc.org/)提供了不同的安装包，建议下载*v2.0*以上的版本进行安装。下面以`ubuntu`64位为例：

下载链接：[https://github.com/jgm/pandoc/releases/download/2.10/pandoc-2.10-1-amd64.deb](https://github.com/jgm/pandoc/releases/download/2.10/pandoc-2.10-1-amd64.deb)

下载完毕后在终端中输入以下命令安装`pandoc`
```bash
sudo dpkg -i pandoc*
```
安装完毕后，可以测试`pandoc`是否运行正常
```bash
pandoc --version
```

### 更换渲染引擎

```bash
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-pandoc --save
```

{% note info %}
如果已经安装了`hexo-renderer-kramed`，建议将其也同时卸载，只保留`hexo-renderer-pandoc`。
{% endnote%}

### 自动化部署

在**Github Actions**中也可以利用`pandoc`作为渲染引擎，在`main.yml`文件中加入以下代码即可：
```yaml
steps:
    - name: 安装pandoc
    run: |
        wget https://github.com/jgm/pandoc/releases/download/2.7/pandoc-2.7-1-amd64.deb
        sudo dpkg -i ./pandoc-2.7-1-amd64.deb
    
    - name: 安裝 Hexo CI
    run: |
        export TZ='Asia/Shanghai'
        npm install hexo-cli -g && npm uninstall hexo-renderer--marked --save && npm install hexo-renderer-pandoc --save
```
完整的`main.yml`配置可以查看[main.yml](https://github.com/Sophistt/sophistt.github.io/blob/hexo/.github/workflows/main.yml)。

# 参考

- [Hexo下mathjax的转义问题](https://segmentfault.com/a/1190000007261752)
- [Hexo博客系统使用Mathjax渲染数学公式](http://rennesong.com/2020/06/08/hexo-math-support-tuto/)