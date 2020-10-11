---
title: VIM 的主题配色与语法高亮设置
date: 2019-09-11 10:50:59
tags: vim
categories: Writing
---

在 **VIM** 编辑器中，想要源代码语法高亮，不仅需要有配色方案，还需要有对应的 syntax 文件。

# 配色方案

**VIM** 中，所有的配色方案都可以存放到 `~/.vim/colors` 文件夹下, 以 `[name].vim` 文件的形式存储。
配色方案中存储的是针对所有文件的基础语法高亮的配色。

以 `Monokai.vim` 为例，将网上下载到的配色方案放入到 `~/.vim/colors` 文件夹下后，在 `~/.vimrc` 中加入以下内容：
```bash
syntax on  "Open syntax highlight"
set background=dark "Set color of background"
colorscheme Monokai "Set colorscheme"
```

# 语法高亮

但是，只设置了配色方案的话，是无法针对某种类型的源代码进行语法高亮的。
为了实现针对不同语言的源码进行语法高亮，需要有对应的 syntax 文件，所有的 syntax 文件都存放到 `~/.vim/syntax` 文件夹下。

syntax 文件中存放的是**语法通配的规则以及何种语法对应哪种配色方案**。
以 **Python** 为例，从 Github 上可以下载到已经配置好的 `python.vim` 文件，将其放入到 `~/.vim/syntax` 文件夹下后，在 `~/.vimrc` 中加入以下内容：
```bash
let python_highlight_all = 1
filetype plugin indent on
hi Comment cterm=italic "Set comment to italics"
```
完成上面的设置后，即可实现对应类型的源码的语法高亮。

# 个人配置

下面是强烈推荐的配色方案以及**Python**的通配文件：

配色方案： [space-vim-theme](https://github.com/liuchengxu/space-vim-theme/blob/master/colors/space_vim_theme.vim) 
通配文件： [python.vim](https://github.com/Sophistt/Small_Projects/blob/master/vim_configuration/syntax/python.vim) 

# References

- [space-vim-theme](https://github.com/liuchengxu/space-vim-theme) 
- [Syntax highlighting in vim for python](https://stackoverflow.com/questions/4746579/syntax-highlighting-in-vim-for-python)