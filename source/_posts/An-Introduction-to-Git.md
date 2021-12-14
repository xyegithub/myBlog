---
title: An Introduction to Git
date: 2021-12-10 21:55:33
summary:
categories:
- Little Things
- Git
tags:
- Git
---





介绍git里面的一些基本概念，了解git运行的基本原理。

<!-- more -->

![The Structure of Git](git.jpg)

git checkout用于切换分支或恢复工作数文件，它是一个危险的命令，因为这条命令会重写工作区。

git ls-files查看缓存区中文件信息，它的参数有，括号里面是简写

--cached (-c) 查看缓存区中所有文件

--midified (-m)查看修改过的文件

--delete (-d)查看删除过的文件

--other (-o)查看没有被git跟踪的文件

