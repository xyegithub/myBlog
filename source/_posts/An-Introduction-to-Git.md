---
title: An Introduction to Git
date: 2021-12-10 21:55:33
description: 介绍git里面的一些基本概念，了解git运行的基本原理。
summary:
categories:
  - Programming
  - Git
tags:
  - Git
---

![The Structure of Git](git.jpg)

git checkout 用于切换分支或恢复工作数文件，它是一个危险的命令，因为这条命令会重
写工作区。

git ls-files 查看缓存区中文件信息，它的参数有，括号里面是简写

--cached (-c) 查看缓存区中所有文件

--midified (-m)查看修改过的文件

--delete (-d)查看删除过的文件

--other (-o)查看没有被 git 跟踪的文件

# errors

## Failed to connect to github.com port 443: Time out

The following commands fixed this issue.

```fish
git config --global http.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080
git config --global --unset http.proxy
git config --global --unset https.proxy
```

# Get back to an old version

`git log` can show the history of your commit.

`git reset xxx` will git back to an old version. However, the workspace will not
change, i.e., the workspace is also the current workspace.
`git reset --hard xxx` will get back to an old version, and the workspace will
be also the old version.

To get back to the newest version `git reflog` can show the reference logs
information. It records when the tips of branches and other references were
updated in the local repository. The code of the newest version will be
observed.

# Clone to slow

The network speed of `git clone` is often very slow in China mainland. To
improve the speed, I often `clone` by ssh, i.e., use the ssh link instead of
http link. However, it need to change the link. Use
`git config --global url. xxx insteadof xxx` can download repos fast without
changing the links. It will write some thing in the `~/.gitconfig` file.

When the `.gitconfig` file is

```
[user]
	name = xyegithub
	email = xye@bupt.edu.cn
[url "https://gitclone.com/"]
	insteadOf = https://github.com
```

`git clone https://github.com/XXX/XXX.git` will be clone from
`https://gitclone.com//XXX/XXX.git/`. When the `.gitconfig` file is

```
[user]
	name = xyegithub
	email = xye@bupt.edu.cn
[url "https://gitclone.com/github.com"]
	insteadOf = https://github.com
```

`git clone https://github.com/XXX/XXX.git` will be clone from
`https://gitclone.com/github.com/XXX/XXX.git/`.

Thus, you can find some mirrors of github and figure out how to use change the
link for `url` in `.gitconfig` file.
