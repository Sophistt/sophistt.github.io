---
title: Configuration and Bundle Management of vim
date: 2019-06-24 11:00:04
tags: vim
categories: Writing
---


Vim是一个高度可配置的文本编辑器，旨在使创建和更改任何类型的文本非常高效。在大多数UNIX系统和Apple OS X中，它都包含为“ vi”。

# Configuration

## Vim 安装

Ubuntu 默认安装了vim-tiny，在终端中输入`vim --version`可以查看当前vim的版本。要安装完整版的vim，在bash中输入
`sudo apt-get install vim-basic` 即可。

## Vim 更新
目前最新的vim 版本是8.1,在终端中**添加ppa源**后即可更新vim。
```bash
sudo add-apt-repository pa:jonathonf/vim
sudo apt update
sudo apt install vim
```

## Vim 基础配置

创建`~/.vimrc`，在该文件内加入相关vim的配置。
```bash

""""""""""""""""""""""""
"Personal configuration"
""""""""""""""""""""""""
"Remove consistency of vi"
set nocompatible
set number "Display row number"
"Hide rolling"    
set guioptions-=r 
set guioptions-=L
set guioptions-=b
set showtabline=0 "Hide top bar"
set guifont=Monaco:h13  "Set font"  
syntax on   "syntax highlight"
set nowrap  "Don't hide lines automatically"
set fileformat=unix "Save file in unix format"
set cindent     "ÉèÖÃCÑùÊ½µÄËõ½ø¸ñÊ½"
set autoindent
set showmatch   "Show the matching pairs"
set scrolloff=5     
set laststatus=2    "Set commad line as two lines"
set fenc=utf-8      "Unicode"
set backspace=2
set mouse=a     "Use mouse"
set selection=exclusive
set selectmode=mouse,key
set matchtime=5
set incsearch
set hlsearch        "Highlight the searching item"
set noexpandtab     "Prohibit extend table"
set whichwrap+=<,>,h,l
set autoread
set cursorline      "Highlight current row"
set cursorcolumn        "Highlight current column"
```

## Vim Pyhton 配置
```bash
""""""""""""""""""""""""
"Python setting"
""""""""""""""""""""""""
au BufNewFile,BufRead *.py
\set tabstop=4
\set softtabstop=4
\set shiftwidth=4
\set textwidth=100
\set expandtab
```

## Vim 主题配置

vim 的配色方案文件存放在`~/.vim/colors`文件夹中（没有则创建一个）。以[**space-vim-dark**](https://github.com/liuchengxu/space-vim-dark/tree/master/colors)配色方案为例，
下载**space-vim-dark.vim**存放到`~/.vim/colors`文件夹内，然后修改`~/.vimrc`启用该配色方案。
```bash
set background=dark     "ÉèÖÃ±³¾°É«"
colorscheme space-vim-dark
"change comment color"
"highlight Comment ctermfg=green guifg=green"
```

# Insert Template

如果想要在新建某类型的文件(如.cpp, .sh)的时候自动插入模板，可以在 `~/.vimrc` 里面采用 `autocmad` 命令进行设置。

## C/C++ 模板

```bash
autocmd BufNewFile *.[ch],*.hpp,*.cpp exec ":call SetTitle()" 

" Title function 
func SetTitle()
	call SetComment()
	if expand("%:e") == 'hpp' 
		call append(line(".")+10, "#ifndef _".toupper(expand("%:t:r"))."_H") 
		call append(line(".")+11, "#define _".toupper(expand("%:t:r"))."_H") 
		call append(line(".")+12, "#ifdef __cplusplus") 
		call append(line(".")+13, "extern \"C\"") 
		call append(line(".")+14, "{") 
		call append(line(".")+15, "#endif") 
		call append(line(".")+16, "") 
		call append(line(".")+17, "#ifdef __cplusplus") 
		call append(line(".")+18, "}") 
		call append(line(".")+19, "#endif") 
		call append(line(".")+20, "#endif //".toupper(expand("%:t:r"))."_H") 
 
	elseif expand("%:e") == 'h' 
	  	call append(line(".")+10, "#pragma once") 
	elseif expand("%:e") == 'c' 
	  	call append(line(".")+10,"#include \"".expand("%:t:r").".h\"") 
	elseif expand("%:e") == 'cpp' 
	  	call append(line(".")+10, "#include \"".expand("%:t:r").".h\"") 
	endif
endfunc

" Comment function 
func SetComment()
	call setline(1,"/*================================================================") 
	call append(line("."),   "*   Copyright (C) ".strftime("%Y")." Sangfor Ltd. All rights reserved.")
	call append(line(".")+1, "*   ") 
	call append(line(".")+2, "*   @file:" .expand("%:t")) 
	call append(line(".")+3, "*   @author: Sophistt")
	call append(line(".")+4, "*   @date:" .strftime("%Y-%m-%d %H:%M")) 
	call append(line(".")+5, "*   @description: ") 
	call append(line(".")+6, "*")
	call append(line(".")+7, "================================================================*/") 
	call append(line(".")+8, "")
	call append(line(".")+9, "")
endfunc
```

## sh 模板

```bash
" Insert title for *.sh file
autocmd BufNewFile *.sh call AddFileInformation_sh()
function AddFileInformation_sh()
      let infor = "#!/bin/bash\n"
      \."\n"
      \."# ***************************************************************************\n"
      \."# * @Copyright (c)  all right reserved \n"
      \."# * \n"
      \."# * @file:".expand("%")." \n"
      \."# * @author: Sophistt \n"
      \."# * @date:".strftime("%Y-%m-%d %H:%M")." \n"
      \."# * @description: Shell script \n"
      \."#* \n"
      \."#**************************************************************************/ \n"
      \."\n"
      \."\n"
      \."\n"
      \."\n"
      \."exit 0"
      silent  put! =infor
endfunction
```

# Bundle Management

Vundle 是 Vim 的插件管理器，通过Vundle可以方便的安装和管理其他各种好用的vim 插件。

## Vundle 安装

在终端中输入以下指令克隆vundle仓库到本地。
```bash
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```
然后加入下面的内容到`~/.vimrc`，添加vundle支持。最后在终端中打开vim，输入`:PluginInstall`完成vundle插件安装。
```bash
"""""""""""""""""""""
"Vundle"
"""""""""""""""""""""
filetype off
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
Plugin 'VundleVim/Vundle.vim'
call vundle#end()
filetype plugin indent on
```

## 利用 Vundle 管理插件

利用 vundle 安装别的插件，只需要在`~/.vimrc`中的 vundle 相关配置的位置加入插件仓库，然后打开 vim，输入`:PluginInstall`即可。<br>
以 **NerdTree** 为例：
```bash
Plugin 'scrooloose/nerdtree'
```
添加上述代码到`~/.vimrc`的合适位置，然后打开 vim ，输入`:PluginInstall`即可完成安装。

# Bundle Recommended

## vim-powerline

**vim-powerline** 是一款美化状态栏的插件，安装后可以看到好看的状态栏显示当前文件名，输入模式等。
```bash
Plugin 'Lokaltog/vim-powerline'
```

## NerdTree

**NerdTree** 是一款目录树插件，安装后可以在 vim 内打开目录树。
```bash
Plugin 'scrooloose/nerdtree'
```
可以加入以下内容到`~/.vimrc`修改快捷键和忽略显示的文件后缀。
```bash
""""""""""""""""""""""""""
"NERDTree"
""""""""""""""""""""""""""
let NERDTreeIgnore=['\~$', '\.pyc$', '\.swp$']
map <C-n> :NERDTreeToggle<CR>
```

## auto-pairs 

**auto-pairs**是自动匹配括号的插件，可以显示当前括号对应的另外一般括号。
```bash
Plugin 'jiangmiao/auto-pairs'
```

## YouCompleteMe

**YouCompleteMe** 是 vim 下很好用的一款自动补全插件，但是其配置难度也很高，喜欢折腾的人可以配置，否则可以使用别的自动补全插件。

### 安装 YouCompleteMe

在`~/.vimrc`中加入`Plugin 'Valloric/YouCompleteMe'`，然后执行`:PluginInstall`进行安装。

### 更新 YouCompleteMe

在安装完成后，可能会出现 *no module named future* 等报错，此时需要进入到`~/.vim/bundle/YouCompleteMe`文件夹内，执行以下命令。
```bash
git submodule update --init --recursive
```
执行完后，打开 vim 后无 *no module named ...* 的报错则成功。

### 编译 YouCompleteMe

在未编译前，打开 vim 会有提示 *YouCompleteMe Server* 的错误，此时只需要在`~/.vim/bundle/YouCompleteMe`的目录下执行编译指令。
加入`--clang-completer` 是添加**C++**支持。编译成功后，打开 vim 应该再无报错的信息。
```bash
./install.py --clang-completer
```
{% note info %}
编译过程中，可能出现 *Download libclang-...-tar.gz2 error* 等下载的错误，这是因为网络的原因。
**解决方法：** 打开出现在错误信息中的链接，直接下载然后移动到 `./third_party/ycmd/` 对应的目录下，然后重新编译。
{% endnote %}

### 启用 YouCompleteMe

将 YouCompleteMe 相关配置加入到`~/.vimrc`中。

```bash
""""""""""""""""""""""""""
"YouCompleteMe Setting"
""""""""""""""""""""""""""
"Set ycm_extra_conf path"
let g:ycm_global_ycm_extra_conf = '~/.ycm_extra_conf.py'
"Do not ask whether loading ycm_extra_conf automatically again"
let g:ycm_confirm_extra_conf=0
set completeopt=longest,menu
"Set python interpreter"
let g:ycm_path_to_python_interpreter='/usr/bin/python'
"Set open auto-completer"
let g:ycm_seed_identifiers_with_syntax=1
"Set open auto-completer in comments"
let g:ycm_complete_in_comments=1
let g:ycm_collect_identifiers_from_comments_and_strings = 0
"Set the number of characters for situation beginning auto-complete"
let g:ycm_min_num_of_chars_for_completion=2
"Set closing window automatically after auto-complete"
let g:ycm_autoclose_preview_window_after_completion=1
"Forbid cache"
let g:ycm_cache_omnifunc=0
"Set open auto-complete in strings"
let g:ycm_complete_in_strings = 1
"Set closing window automatically after leaving insert mode"
autocmd InsertLeave * if pumvisible() == 0|pclose|endif
```

### Python 自动补全

编译通过后，正常情况下已经可以自动补全系统内的 Python 库，但是**无法自动补全如 virtualeenv, conda 等环境内的 Python 库**，需要将他们的路径加入到 **PYTHONPATH** 才可以完成自动补全。修改`~/.bashrc`文件即可。
```bash
export PYTHONPATH='path/to/env/lib/python3.x/site-packages:$PYTHONPATH'
```

### C++ 自动补全

YouCompleteMe 的 C++ 自动补全需要利用到`.ycm_extra_conf.py`文件，当使用 vim 打开一个文件的时候，**YouCompleteMe** 插件会自动在当前目录及其上级目录搜索该文件，我们也可以在`~`目录下创建该文件备用。

复制`~/.vim/bundle/YouCompleteMe/third_party/ycmd/examples/.ycm_extra_conf.py` 到`~`目录下备用。

```bash
cp ~/.vim/bundle/YouCompleteMe/third_party/ycmd/examples/.ycm_extra_conf.py ~/
```
复制后，打开`.cpp`文件已经可以使用部分自动补全的功能，通过修改该文件**flags**部分可以添加自定义头文件路径。头文件的路径有顺序要求，顺序错误可能有错误发生导致无法自动补全，推荐的顺序如下：
```bash
'-x', 'c++',
'-isystem', '/usr/include/c++/7.1.0',
'-isystem', '/usr/include/c++/7.1.0/backward',
'-isystem', '/usr/local/include',
'-isystem', '/usr/include',
```
为了使用自定义的类和头文件，可以将`.ycm_extra_conf.py`复制到 C++ 项目空间内，然后加入头文件路径。

### C++ 自动跳转

YouCompleteMe 的 C++ 带有自动跳转的功能，可以跳转到声明或者定义处，命令为：

```bash
:YouCompleter GoToDeclaration
:YouCompleter GoToDefinition
```
使用命令来进行跳转的话十分繁琐，因此可以使用快捷键映射将这两个命令绑定到常用快捷键下。
```bash
nnoremap gl :YcmCompleter GoToDeclaration<CR>
nnoremap gk :YcmCompleter GoToDefinition<CR>
```

YouCompleteMe 还提供了前后跳转的功能，使用快捷键 `ctrl+o` 和 `ctrl+i` 来前后跳转。

# Configuration File Download

- [<font size="4">.vimrc</font>](https://github.com/Sophistt/Small_Projects/blob/master/vim_configuration/.vimrc)
- [<font size="4">.ycm_extra_conf.py</font>](https://github.com/Sophistt/Small_Projects/blob/master/vim_configuration/.ycm_extra_conf.py)

# References

- [使用Vim插件管理器Vundle](https://www.jianshu.com/p/8d416ac4ad11)
- [space-vim-dark](https://github.com/liuchengxu/space-vim-dark)
- [YouCompleteMe自动补全C语言大型项目 ycm](http://www.voidcn.com/article/p-zgivkggq-ta.html)
