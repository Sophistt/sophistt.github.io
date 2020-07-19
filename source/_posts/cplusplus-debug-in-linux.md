---
title: C++ Debug in Linux through GDB
date: 2019-06-26 17:11:47
tags: 
categories: 
  - Programming
  - C++
---

# Prerequisite

## 修改 CMakelists.txt

Linux 下为了使用 GDB 调试 C++ 项目，在编译的过程中需要加入 `-g -ggdb` 参数，而习惯上我们使用 CMakelists 来生成 `Makefile` , 因此我们需要修改 **CMakelists.txt** 来生成符合条件的 `Makefile` 。

首先在 **CMakelists.txt** 中加入以下内容：
```cmake
# The configuration used for construction of Debug Makefile
SET(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")

# The configuration used for construction of Release Makefile
SET(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")
```
加入这两行的之后，我们就可以分别用不同的命令调用不同的`CMAKE_CXX_FLAGS`生成不同的`Makefile`。

## 编译

使用下面的指令生成用于 **Debug** 的`Makefile`:
```bash
cmake -DCMAKE_BUILD_TYPE=Debug 
```
然后用`make`指令生成可执行文件即可。

# Debug

## 开始调试

进入到可执行文件的目录下，假设可执行文件名为 **TestClass**, 输入以下命令则进入调试模式：
```bash
gdb TestClass
```
如果没有错误的话，应当可以看到以下输出：
![Imgur](https://i.imgur.com/ul8ytpA.png)

## 窗口相关指令

gdb 自带的显示界面为 **TUI** , 通过在gdb中输入不同的指令可以开启多个窗口查看不同的内容，方便调试，常用的命令为：

  | 命令 | 功能 | 
  | :--: | :------------:  | 
  | `layout src`   | 显示源代码窗口       | 
  | `layout regs`  | 显示寄存器窗口    | 
  | `layout asm`   | 显示汇编代码窗口     | 
  | `layout split` | 显示源代码和汇编窗口 |
  | `refresh`      | 刷新所有窗口        | 
  | `focus cmd/src`| 切换当前关注窗口    |

## 程序运行指令

  | 命令 | 功能 | 
  | :--: | :------------:  | 
  | `r`  | 运行程序       | 
  | `c`  | 运行程序到下一个断点  | 

当程序开始运行后，我们可以使用下面的指令逐步运行程序。
{% note warning %}
必须是程序运行起来后，才能逐步运行程序，否则会出现 **The program is not being run** 的错误。
{% endnote %}

  | 命令 | 功能 | 
  | :--: | :------------:  | 
  | `n`  | 单行运行       | 
  | `s`  | 单步运行  | 

## 断点

我们可以设置，查看，删除断点来帮助调试程序。

  | 命令 | 功能 | 
  | :--: | :------------:  | 
  | `b {number of line}` | 在某行设置断点       | 
  | `b {function name}`  | 在函数开始处设置断点  | 
  | `info b `            | 查看断点  |
  | `d`                  | 删除所有断点  |
  | `d {nubmer}`         | 删除指定断点  |
  | `enable {nubmer}`    | 启用指定断点  |
  | `disable {nubmer}`   | 禁止指定断点  |

## 显示相关指令

指令 `l` 可以在窗口显示当前 10 行的源代码， 如果出现 **no read file** 的报错，是因为在 `cmake` 的时候没有选择加入 `-DCMAKE_BUILD_TYPE=Debug` 的选项导致生成的可执行文件不能用于调试。 
同时，我们还可以利用`p`打印变量的值，或者利用`display`方便地跟踪查看变量, 和`x`查看内存的值。

  | 命令 | 功能 | 
  | :--: | :------------:  | 
  | `p {variables}`  | 打印输出某个变量的值       | 
  | `display {variables}`  | 跟踪该变量，每次运行都输出该变量的值  | 
  | `undisplay {number}`   | 取消跟踪该变量  | 
  | `x {address}`    | 跟踪该内存地址的值  | 

# References

- [CMake生成可用gdb调试的可执行文件](https://blog.csdn.net/lemonaha/article/details/72837561)
- [GDB调试指南(初级)](https://blog.csdn.net/LF_2016/article/details/54172864)
- [gdb查看源代码](https://blog.csdn.net/hiawui/article/details/6173107)

