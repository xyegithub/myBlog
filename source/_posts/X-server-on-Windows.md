---
title: X Server on Windows
top: false
cover: false
toc: true
date: 2022-05-05 09:47:44
password:
summary:
description: Use X server on  Windows
categories:
  - Programming
  - X server
tags:
  - X server
---

# On the Windows side: Xming

I also tried VcXsrv. However, it failed to be launch. Thus I installed
[xming](https://sourceforge.net/projects/xming/). After installing, notice that
`No Access Control` may should be picked when start `Xlaunch`. Then a progress
`xming server` will be started (see it in your task bar).

Windows Side Done.

# On the linux side: $DISPLAY

The DISPLAY variable should be set as `:0.0`. For bash shell, write
`export DISPLAY=:0.0` in your `.bashrc`. For fish shell, write
`set -x DISPLAY :0.0` in your fish configuration file.

Then you can run a gui program on your linux and get the window on the Windows
side.
