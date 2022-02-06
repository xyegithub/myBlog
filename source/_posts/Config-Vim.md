---
title: Config Vim
top: false
cover: false
toc: true
mathjax: true
date: 2022-01-20 10:24:34
password:
summary:
description:  vim的配置，包括markdown, vimtex的支持等
categories:
- Little Things
tags:
- vim
- markdown
- vimtex
---

# 安装anaconda
本来安装anaconda应该问题不大，但是却发现了一个问题。
`./Anaconda3-2021.11-Linux-x86_64.sh: 489: [[: Exec format error`
这个错，是因为直用./Anaconda.sh启动安装默认用的是sh
改成了bash Anaconda.sh就不会报错了.

# 一些碎的Linux知识

## 查找包的安装位置
一些软件用apt install了，但是不知道安装位置在哪里
`dpkg -L xxx`显示包的安装位置．

## 编译错误定位
编译的时候,打印一些log但是并不详细
一般会有更加详细的编译log文件，在里面可以更精确的定位错误发生的原因．

## linux 的环境变量可能是两个
`LIBRARY_PATH`编译的时候用到的库的搜索路径
`LD_LIBRARY_PATH`程序加载时库的搜索路径

## 遇到一个问题明明再搜索路径下有动态库，却报错所无法找到
okular: error while loading shared libraries: libQt5Core.so.5: cannot open shared object file: No such file or directory
用strip处理完之后解决
sudo strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.so.5

# 编译vim
apt安装的vim不能随意的控制vim的特性，比如在latex反向搜索的时候需要的+clietservice。
apt-cache search libc-dev
ln -s libXtst.so.6 libXtst.so
综合两篇博客。
https://www.jianshu.com/p/aa5ea81bbc72
https://toutiao.io/posts/runvgs/preview
尽量多的保留特性，最终得到的config 命令是
```
./configure --with-features=huge \
    --enable-multibyte \
    --enable-rubyinterp=yes \
    --enable-python3interp=yes \
    --enable-perlinterp=yes \
    --enable-cscope \
    --enable-fontset \
    --enable-largefile \
    --enable-fail-if-missing \
    --prefix=/path-to-install
```
`--enable-fail-if-missing` 用于显示错误信息。

# 有点搞笑的是编译了很久没有成功，最后apt安装解决了

编译的时候feature 用了hug，但是还是没有增加+clientserver。
我估计是缺少库。查src/auto/cofig.log也没有发现很相关的信息。
最后`sudo apt-get install vim-gtk`成功安装了+clientserver的vim。






