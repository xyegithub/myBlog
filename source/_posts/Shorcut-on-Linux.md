---
title: Shorcut on Linux
top: false
cover: false
toc: true
mathjax: true
date: 2022-02-10 09:22:15
password:
summary:
description: Describe the shortcuts of linux and how to set them
categories:
- Little Things
- Linux
tags:
- Linux
---


# stty () #

# gun readline - get a line from a user with editing #

## Synopsis ##


```c
#include <stdio.h>
#include <readline/readline.h>
#include <readline/history.h>

char *
readline (const char *prompt)

```


## Description ##

**readline will read a line from the terminal and return it, using
*prompt* as a prompt.**

If prompt is NULL or the empty string, no prompt is issued.
The line returned is allocated with `malloc`; the caller must
free it when finished. The line returned has the final newline removed,
so only the text of the line remains.

## Return Value ##

readline returns the text of the line read. A black line returns the empty
string. If EOF is encountered while reading a line, and the line is empty,
NULL is returned. If an EOF is read with a non-empty line, it is treated
as a newline.

## Initialization file ##

Readline is customized by putting commands in an initialization file
(the `inputrc` file). The name of this file is taken from the value of 
the INPUTRC environment variable. If that variable is unset, the default
is `~/.inputrc`. If that file does not exist or cannot be read, the
ultimate default is `/etc/inputrc`. 

When a program which uses the readline library starts up, the init
file is read, and the key bindings and variables are set. 

There are only a few basic constructs allowed in the readline init file.

1. Black lines are ignored.
2. Lines beginning with a `#` are comments.
3. Lines beginning with a `$` indicate conditional constructs.
4. Other lines denote key bindings and variable settings.



Each program using this library may add its own commands and bindings.


For example, placing
```bash
M-Control-u: universal-argment
```
into the `inputrc` would make `M-C-u` execute the readline command
`universal-argument`.

