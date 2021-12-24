---
title: Experiments
top: false
cover: false
toc: true
mathjax: true
date: 2021-12-17 10:00:56
password:
summary:
description: 发现统计规律，记录一些自己diy的实验。
categories:
- Experiments
tags:
- Personal Thought
- Experiments
- private
---



# Deep Learning

## Feature Map Multiplication

[source code](https://github.com/xyegithub/Featrue-map-multiplication)

dataset: Caltech101

**shortcut使用bn，而Res分支使用sigmoid的情况。**

| 配置                                                         | accuracy |
| ------------------------------------------------------------ | -------- |
| `out = self.bn(out) + self.shortcut(x) `                     | 87.62    |
| `out = (out.sigmoid() + 1)* self.bn_s(self.shortcut(x))`     | 78.23    |
| `out = (self.bn(out).sigmoid() + 1) * self.bn_s(self.shortcut(x))` | 78.69    |
| `(self.bn(out).sigmoid() + 0.5) * self.bn_s(self.shortcut(x)) ` | 78.57    |
| `self.bn_s.bias.data[:]=1`<br>`out = (self.bn(out).sigmoid() + 1) * self.bn_s(self.shortcut(x))    ` | 82.26    |
| `self.bn_s.bias.data[:]=1`  <br>`out = (self.bn(out).sigmoid() + 0.5) * self.bn_s(self.shortcut(x)) ` | 84.10    |
| ` self.bn.bias.data[:]=0`<br>`self.bn_s.bias.data[:]=1`<br>` out = (self.bn(out).sigmoid() + 0.5) * self.bn_s(self.shortcut(x))             ` | 84.85    |
| `self.bn.bias.data[:]=0`<br>`self.bn_s.bias.data[:]=1`<br>`out = (self.bn(out).sigmoid()) * self.bn_s(self.shortcut(x))` | 85.83    |
| `self.bn.bias.data[:]=0`<br>`self.bn.weight.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`out = (self.bn(out).sigmoid()) * self.bn_s(self.shortcut(x))` | 81.57    |

1. 从这个结果看出shortcut的均值为1，会使得优化更好
2. Res分支的sigmoid不需要加0.5或者1，性能提高了。乘以sigmoid本身有恒等的特性。sigmoid分支输出都为0时，sigmoid输入都是0.5。
3. 在Res分支的sigmoid之前，先对out进行bn归一化，会优化的更好，而且让归一化的均值为0，会优化的更好。但是如果同时也控制归一化的方差，效果变差。无参的bn限制了表达能力。

**shortcut使用bn，而Res分支使用sigmoid的情况。**

| 配置                                                         | accuracy |
| ------------------------------------------------------------ | -------- |
| `out = self.bn(out) + self.shortcut(x) `                     | 87.62    |
| `out = (self.shortcut(x).sigmoid() + 1)* self.bn_s(out)`     | 83.93    |
| `out = (self.bn(self.shortcut(x)).sigmoid() + 1) * self.bn_s(out)` | 85.54    |
| `(self.bn(self.shortcut(x)).sigmoid() + 0.5) * self.bn_s(out) ` | 85.14    |
| `self.bn_s.bias.data[:]=1`<br>`out = (self.bn(self.shortcut(x)).sigmoid() + 1) * self.bn_s(out)    ` | 85.43    |
| `self.bn_s.bias.data[:]=1`  <br>`out = (self.bn(self.shortcut(x)).sigmoid() + 0.5) * self.bn_s(out) ` | 87.44    |
| ` self.bn.bias.data[:]=0`<br>`self.bn_s.bias.data[:]=1`<br>` out = (self.bn(self.shortcut(x)).sigmoid() + 0.5) * self.bn_s(out)             ` | 84.39    |
| `self.bn.bias.data[:]=0`<br>`self.bn_s.bias.data[:]=1`<br>`out = (self.bn(self.shortcut(x)).sigmoid()) * self.bn_s(out)` | 84.39    |
| `self.bn.bias.data[:]=0`<br>`self.bn.weight.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`out = (self.bn(self.shortcut(x)).sigmoid()) * self.bn_s(out)` | 84.91    |

**shortcut使用bn，而Res分支使用bn的情况。**

| 配置                                                         | Accuracy |
| ------------------------------------------------------------ | -------- |
| `out = self.bn(out)* self.bn_s(self.shortcut(x))`            | 77.13    |
| `self.bn_s.bias.data[:]=1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x)) ` | 75.75    |
| `self.bn.bias.data[:] = 1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x))` | 79.55    |
| `self.bn.bias.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x))` | 81.39    |
| `self.bn.bias.data[:]=1`<br>`self.bn.weight.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x))` | 81.05    |
| `self.bn.bias.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`self.bn_s.weight.data[:]=1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x))` | 80.24    |
| `self.bn.bias.data[:]=1`<br>`self.bn.weight.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`self.bn_s.weight.data[:]=1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x))` | 81.22    |

均值为1的话，一般来说还是会获益。但是bn的效果不是很好。

**shortcut使用sigmoid，而Res分支使用sigmoid的情况。**

| 配置                                                         | Accuracy |
| ------------------------------------------------------------ | -------- |
| `out = (self.shortcut(x).sigmoid()) * out.sigmoid())`        | 79.44    |
| `out = (self.shortcut(x).sigmoid()) * (out.sigmoid() + 0.5)` | 70.05    |
| `out = (self.shortcut(x).sigmoid() + 0.5) * (out.sigmoid())` | 76.61    |
| `out = (self.shortcut(x).sigmoid()) * (out.sigmoid() + 1)`   | 72.64    |
| `out = (self.shortcut(x).sigmoid() + 1) * (out.sigmoid())`   | 71.77    |

没有加bn，sigmoid会过饱和，效果不是很好。一边加bn

| 配置                                                         | Accuracy |
| ------------------------------------------------------------ | -------- |
| `out = (self.shortcut(x).sigmoid()) * self.bn(out).sigmoid()` | 86.69    |
| `out = (self.bn_s(self.shortcut(x)).sigmoid()) * out.sigmoid()` | 79.90    |
| `self.bn_s.bias.data[:]=0`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * out.sigmoid()` | 76.32    |
| `self.bn.bias.data[:]=0`<br>`out = (self.shortcut(x).sigmoid()) * self.bn(out).sigmoid()` | 83.99    |
| `self.bn.bias.data[:]=0 `<br>` out = (self.shortcut(x).sigmoid()) * (self.bn(out).sigmoid() + 0.5)` | 86.52    |
| `self.bn.bias.data[:]=0 `<br>`out = (self.shortcut(x).sigmoid()) * (self.bn(out).sigmoid() + 1) ` | 82.83    |
| `self.bn.weight.data[:]=1`<br>`out = (self.shortcut(x).sigmoid()) * self.bn(out).sigmoid()` | 78.74    |
| `self.bn_s.bias.data[:]=0`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid() + 0.5) * out.sigmoid()` | 73.39    |

两边加bn

| 配置                                                         | Accuracy |
| ------------------------------------------------------------ | -------- |
| ` out = (self.bn_s(self.shortcut(x)).sigmoid()) * self.bn(out).sigmoid()` | 86.23    |
| `self.bn.bias.data[:]=0`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * self.bn(out).sigmoid()` | 86.41    |

