---
layout: post
title: "Bash命令行编辑及编码风格约定"
date: 2012-08-01 21:00
comments: true
categories: bash shell
---

## Bash命令行编辑与Readline


近年来看了不少介绍Linux命令行和shell编程方面的书，包括我翻译的这本[《Linux命令行与shell脚本编程大全（第2版）》](http://book.douban.com/subject/11589828/)。尽管这些书都中规中矩地介绍了命令行常用的命令、shell的启动配置、shell的环境变量及shell编程的种种，但鲜有书肯花一定的篇幅来介绍跟shell交互时命令行编辑方面的内容，而命令行编辑在日常的命令行使用中恰恰非常重要。bash的命令行编辑功能由[Readline](http://cnswww.cns.cwru.edu/php/chet/readline/rltop.html)提供。很多脚本语言的交互式shell的行编辑功能也都由该库提供。比如Python和gnuplot的交互式shell。但由于许可证（Readline采用GPLv3）带来的授权方面的限制，有些脚本语言的交互式shell采用其他一些行编辑库，如**[libedit](http://www.thrysoee.dk/editline/)**、**[Tecla](http://www.astro.caltech.edu/~mcs/tecla/)**等等。也有出于其他考虑使用自有行编辑库的shell，比如zsh使用的就是自己的[zle](http://zsh.sourceforge.net/Guide/zshguide04.html)（所以zsh的自动补全机制跟bash的也有所不同）。

> 注意：这里说的命令行编辑（Commandline Editing）跟命令行编辑器（Commandline Text Editor）不同。命令行编辑是指在交互式命令行上对输入的命令进行编辑，而命令行编辑器则是指可在命令行界面中使用的用来编辑文本文件的编辑器，比如**Vim**或**Emacs**都是命令行编辑器。而sed和awk虽然也能处理文本，但它们是用来对文本流进行处理的，所以称为流编辑器（Stream Editor）。

**Readline**主要提供的命令可分为以下几类：

- 光标移动命令
- 历史记录命令
- 文本修改命令
- 文本复制和删除命令
- 自动补全命令
- 键盘宏命令
- 其他命令

具体每个分类都包含哪些命令，可以参考[bash官方文档中相关的介绍](http://www.gnu.org/software/bash/manual/bashref.html#Readline-Interaction)。这里就每个分类举几个例子。


### 光标移动命令


- 移到行首：`ctrl + a`
- 移到行尾：`ctrl + e`
- 向前移动一个字符：`ctrl + f`
- 向后移动一个字符：`ctrl + b`
- 清空屏幕：`Ctrl + l`


### 历史记录命令


- 前一历史命令：`ctrl + p`
- 后一历史命令：`ctrl + n`
- 逆向搜索历史命令：`ctrl + r`
- 向前搜索历史命令：`ctrl + s`


### 文本修改命令


- 删除当前光标下字符：`ctrl + d`
- 要输入类似`^Q`这样的按键组合，可以先按下`ctrl + v`，然后再按下`ctrl + q`
- 将光标前面的字符和光标下的字符互换顺序：`ctrl + t`


### 文本复制和删除命令


- 删除光标至行尾的字符：`ctrl + k`
- 删除光标至行首的字符：`ctrl + u`
- 删除光标前的一个单词：`ctrl + w`（以空格作为单词分割符，并会将删掉的内容放到kill ring中）
- 将kill ring顶部的内容复制到在当前位置：`ctrl + y`


### 自动补全命令


- 自动补全：`TAB`
- 列出可能的补全内容是文件名：`ctrl + x /`
- 列出可能的补全内容是用户名：`ctrl + x ~`
- 列出可能的补全内容是变量：`ctrl + x $`
- 列出可能的补全内容是主机名：`ctrl + x @`
- 列出可能的补全内容是命令：`ctrl + x !`


### 键盘宏命令


- 开始录制宏：`ctrl + x (`
- 结束录制宏：`ctrl + x )`
- 运行宏：`ctrl + x e`


### 其他命令


- 重新读取配置文件：`ctrl + x` `ctrl + r`
- 撤销前面的操作：`ctrl + x` `ctrl + u`
- 设置标记：`ctrl + @`
- 交换标记和光标的位置：`ctrl + x` `ctrl + x`
- 显示当前shell的版本信息：`ctrl + x` `ctrl + v`
- 启动编辑器编辑命令，编辑完成后执行：`ctrl + x` `ctrl + e`


## Bash编码风格规范


正是Bash跟Linux系统的深度融合，所以一直以来人们都将Bash当成一门不严肃的语言。有时候甚至只是当作一个用来将命令记录下来的工具。如果你只是要用Linux做一些简单的配置和系统管理，那采取这种做法还姑且过得去。而在要构建复杂的工具时，人们通常会借助Python或Ruby一类编程语言来构建脚本，它们能够提供更高级的数据类型供程序使用。但如果你要采用bash来构建较大型的命令行工具时，你就必须遵循一定的编码风格规范。如果你们的项目还没有明确的编码风格规范，那可以参考[这份编码规范](http://lug.fh-swf.de/vim/vim-bash/StyleGuideShell.en.pdf)。

