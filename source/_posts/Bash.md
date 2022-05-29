---
title: Bash
top: false
cover: false
toc: true
date: 2022-05-12 11:28:51
password:
summary:
description: Bash Shell
categories:
  - Programming
  - Shell
  - Bash
tags:
  - Bash
---

# Arguments

# Some Scripts

## Modify the brightness of Arch Linux

Put a number into `/sys/class/backlight/intel_backlight/brightness` represent
the brightness. The max number of brightness is denoted in file
`/sys/class/backlight/intel_backlight/max_brightness`.

```bash
#!/bin/bash
flag=true
max_light=$(cat /sys/class/backlight/intel_backlight/max_brightness)

read -p "please input a number in range (0, $max_light):" num

while $flag
do
  # >name, means 1>name -- redirect stdout to the file name
  # &>name, is like 1>name 2>name -- redirect stdout and stderr to the file name (however, name is only opened once; if you actually wrote 1>name 2>name, it'd try to open name twice and perhaps malfunction.)
  # expr performs the arithmetic operations
  # if the input num is not a number then an error will produce
  # $? can capture the error, if expr is executed correctly, $? == 0, else it is the number represent the error type.

         expr $num + 0 &>/dev/null
         # $ false || echo "Oops, fail"
         # Oops, fail

          # $ true || echo "Will not be printed"
          # $

          # $ true && echo "Things went well"
          # Things went well

          # $ false && echo "Will not be printed"
          # $
          # for && the right command will be executed, only the left command is true
          # for || the left command will be executed, only the left command is false
          # thus when [xxx] is true flag=true will be executed and the last command will
          # not be executed.
          # when [xxx] is false, flag=false will never be executed and the last command
          # will be executed.
         [[ $? -eq 0 && $num -ge 0 && $num -le $max_light ]] && flag=false || read -p "please input a number in range (0, $max_light):" num
done
echo $num > /sys/class/backlight/intel_backlight/brightness
```
