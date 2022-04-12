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
  - Machine Learning
  - Experiments
tags:
  - Personal Thought
  - Experiments
  - private
---

# Deep Learning

## Feature Map Multiplication

### dataset: Caltech101

[source code](https://github.com/xyegithub/Featrue-map-multiplication)

3 号服务器

/media/new*2t/yexiang/image_classification/multiply/from*#1/ffmnst/Caltech101

#### bn，sig

| 配置                                                                                                                                                   | accuracy |
| ------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- |
| `out = self.bn(out) + self.shortcut(x) `                                                                                                               | 87.62    |
| `out = (out.sigmoid() + 1)* self.bn_s(self.shortcut(x))`                                                                                               | 78.23    |
| `out = (self.bn(out).sigmoid() + 1) * self.bn_s(self.shortcut(x))`                                                                                     | 78.69    |
| `(self.bn(out).sigmoid() + 0.5) * self.bn_s(self.shortcut(x)) `                                                                                        | 78.57    |
| `self.bn_s.bias.data[:]=1`<br>`out = (self.bn(out).sigmoid() + 1) * self.bn_s(self.shortcut(x)) `                                                      | 82.26    |
| `self.bn_s.bias.data[:]=1` <br>`out = (self.bn(out).sigmoid() + 0.5) * self.bn_s(self.shortcut(x)) `                                                   | 84.10    |
| ` self.bn.bias.data[:]=0`<br>`self.bn_s.bias.data[:]=1`<br>`out = (self.bn(out).sigmoid() + 0.5) * self.bn_s(self.shortcut(x)) `                       | 84.85    |
| `self.bn.bias.data[:]=0`<br>`self.bn_s.bias.data[:]=1`<br>`out = (self.bn(out).sigmoid()) * self.bn_s(self.shortcut(x))`                               | 85.83    |
| `self.bn.bias.data[:]=0`<br>`self.bn.weight.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`out = (self.bn(out).sigmoid()) * self.bn_s(self.shortcut(x))` | 81.57    |

1. 从这个结果看出 shortcut 的均值为 1，会使得优化更好
2. Res 分支的 sigmoid 不需要加 0.5 或者 1，性能提高了。乘以 sigmoid 本身有恒等的
   特性。sigmoid 分支输出都为 0 时，sigmoid 输入都是 0.5。
3. 在 Res 分支的 sigmoid 之前，先对 out 进行 bn 归一化，会优化的更好，而且让归一
   化的均值为 0，会优化的更好。但是如果同时也控制归一化的方差，效果变差。无参的
   bn 限制了表达能力。

#### sig, bn

| 配置                                                                                                                                                   | accuracy |
| ------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- |
| `out = self.bn(out) + self.shortcut(x) `                                                                                                               | 87.62    |
| `out = (self.shortcut(x).sigmoid() + 1)* self.bn_s(out)`                                                                                               | 83.93    |
| `out = (self.bn(self.shortcut(x)).sigmoid() + 1) * self.bn_s(out)`                                                                                     | 85.54    |
| `(self.bn(self.shortcut(x)).sigmoid() + 0.5) * self.bn_s(out) `                                                                                        | 85.14    |
| `self.bn_s.bias.data[:]=1`<br>`out = (self.bn(self.shortcut(x)).sigmoid() + 1) * self.bn_s(out) `                                                      | 85.43    |
| `self.bn_s.bias.data[:]=1` <br>`out = (self.bn(self.shortcut(x)).sigmoid() + 0.5) * self.bn_s(out) `                                                   | 87.44    |
| ` self.bn.bias.data[:]=0`<br>`self.bn_s.bias.data[:]=1`<br>`out = (self.bn(self.shortcut(x)).sigmoid() + 0.5) * self.bn_s(out) `                       | 84.39    |
| `self.bn.bias.data[:]=0`<br>`self.bn_s.bias.data[:]=1`<br>`out = (self.bn(self.shortcut(x)).sigmoid()) * self.bn_s(out)`                               | 84.39    |
| `self.bn.bias.data[:]=0`<br>`self.bn.weight.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`out = (self.bn(self.shortcut(x)).sigmoid()) * self.bn_s(out)` | 84.91    |

**sig, bn 比 bn, sig 的效果好。原因可能是，sig 本来就有梯度的问题，然而
，shortcut 分支没有需要优化的参数，所以把 sigmoid 放在 shortcut 分支更好？**

#### bn, bn

| 配置                                                                                                                                                                      | Accuracy |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `out = self.bn(out)* self.bn_s(self.shortcut(x))`                                                                                                                         | 77.13    |
| `self.bn_s.bias.data[:]=1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x)) `                                                                                          | 75.75    |
| `self.bn.bias.data[:] = 1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x))`                                                                                           | 79.55    |
| `self.bn.bias.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x))`                                                               | 81.39    |
| `self.bn.bias.data[:]=1`<br>`self.bn.weight.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x))`                                 | 81.05    |
| `self.bn.bias.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`self.bn_s.weight.data[:]=1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x))`                               | 80.24    |
| `self.bn.bias.data[:]=1`<br>`self.bn.weight.data[:]=1`<br>`self.bn_s.bias.data[:]=1`<br>`self.bn_s.weight.data[:]=1`<br>`out = self.bn(out)* self.bn_s(self.shortcut(x))` | 81.22    |

均值为 1 的话，一般来说还是会获益。但是 bn 的效果不是很好。

#### sig, sig

| 配置                                                         | Accuracy |
| ------------------------------------------------------------ | -------- |
| `out = (self.shortcut(x).sigmoid()) * out.sigmoid())`        | 79.44    |
| `out = (self.shortcut(x).sigmoid()) * (out.sigmoid() + 0.5)` | 70.05    |
| `out = (self.shortcut(x).sigmoid() + 0.5) * (out.sigmoid())` | 76.61    |
| `out = (self.shortcut(x).sigmoid()) * (out.sigmoid() + 1)`   | 72.64    |
| `out = (self.shortcut(x).sigmoid() + 1) * (out.sigmoid())`   | 71.77    |

没有加 bn，sigmoid 会过饱和，效果不是很好。一边加 bn

| 配置                                                                                                | Accuracy |
| --------------------------------------------------------------------------------------------------- | -------- |
| `out = (self.shortcut(x).sigmoid()) * self.bn(out).sigmoid()`                                       | 86.69    |
| `out = (self.bn_s(self.shortcut(x)).sigmoid()) * out.sigmoid()`                                     | 79.90    |
| `self.bn_s.bias.data[:]=0`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * out.sigmoid()`       | 76.32    |
| `self.bn.bias.data[:]=0`<br>`out = (self.shortcut(x).sigmoid()) * self.bn(out).sigmoid()`           | 83.99    |
| `self.bn.bias.data[:]=0 `<br>` out = (self.shortcut(x).sigmoid()) * (self.bn(out).sigmoid() + 0.5)` | 86.52    |
| `self.bn.bias.data[:]=0 `<br>`out = (self.shortcut(x).sigmoid()) * (self.bn(out).sigmoid() + 1) `   | 82.83    |
| `self.bn.weight.data[:]=1`<br>`out = (self.shortcut(x).sigmoid()) * self.bn(out).sigmoid()`         | 78.74    |
| `self.bn_s.bias.data[:]=0`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid() + 0.5) * out.sigmoid()` | 73.39    |

两边加 bn

| 配置                                                                                                                                                                 | Accuracy |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ` out = (self.bn_s(self.shortcut(x)).sigmoid()) * self.bn(out).sigmoid()`                                                                                            | 86.23    |
| `self.bn.bias.data[:]=0`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * self.bn(out).sigmoid()`                                                                 | 86.41    |
| `self.bn.bias.data[:]=0`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * (self.bn(out).sigmoid() + 1)`                                                           | 85.83    |
| `self.bn.bias.data[:]=0`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * (self.bn(out).sigmoid() + 0.5)`                                                         | 86.23    |
| `self.bn.bias.data[:]=0`<br>`self.bn_s.bias.data[:]=0`<br/>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * self.bn(out).sigmoid()`                                  | 86.52    |
| `self.bn.bias.data[:]=0`<br/>`self.bn_s.bias.data[:]=0`<br/>`self.bn_s.weight.data[:]=1`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * self.bn(out).sigmoid()` | 82.49    |
| `self.bn.bias.data[:]=0`<br/>`self.bn.weight.data[:]=1`<br/>`self.bn_s.bias.data[:]=0`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * self.bn(out).sigmoid()`   | 83.47    |
| `self.bn.bias.data[:]=0`<br>`self.bn_s.bias.data[:]=0`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * (self.bn(out).sigmoid() + 0.5)`                           | 84.91    |
| `self.bn.bias.data[:]=0`<br/>`self.bn_s.bias.data[:]=0`<br/>`out = (self.bn_s(self.shortcut(x)).sigmoid() + 0.5) * self.bn(out).sigmoid())`                          | 86.92    |
| `self.bn.bias.data[:]=0`<br/>`self.bn_s.bias.data[:]=0`<br/>`out = (self.bn_s(self.shortcut(x)).sigmoid() + 1) * self.bn(out).sigmoid())`                            | 86.75    |
| `self.bn.bias.data[:]=0`<br/>`self.bn_s.bias.data[:]=0`<br/>`out = (self.bn_s(self.shortcut(x)).sigmoid() + 0.5) * (self.bn(out).sigmoid() + 0.5)`                   | 84.79    |
| `self.bn.weight.data[:]=1`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * self.bn(out).sigmoid()`                                                               | 81.91    |
| `self.bn_s.weight.data[:]=1`<br>`out = (self.bn_s(self.shortcut(x)).sigmoid()) * self.bn(out).sigmoid()`                                                             | 80.93    |

#### Resdual 分支内部使用乘法

| 配置                                                                                                                                    | Accuracy |
| --------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `out_1 = F.relu(self.bn2(out_1))`<br>`out *= self.adap(out_1)`                                                                          | 86.06    |
| `out_1 = F.relu(self.bn2(out_1))`<br>`out *= self.adap(out_1).sigmoid()`                                                                | 87.33    |
| `out_1 = self.bn2(out_1)`<br>`out = self.conv2(out_1)`<br>`out *= self.adap(out_1).sigmoid()`                                           | 85.71    |
| `out_1 = self.bn2(out_1)`<br/>`out = self.conv2(out_1.relu())`<br/>`out *= self.adap(out_1).sigmoid()`                                  | 87.85    |
| `self.bn2.bias.data[:]=0`<br>`out_1 = self.bn2(out_1)`<br>`out = self.conv2(out_1)`<br>`out *= self.adap(out_1).sigmoid()`              | 86.64    |
| `self.bn2.bias.data[:]=0`<br/>`out_1 = self.bn2(out_1)`<br/>`out = self.conv2(out_1.relu())`<br/>`out *= self.adap(out_1).sigmoid()`    | 81.51    |
| `self.bn2.bias.data[:]=0`<br/>`out_1 = self.bn2(out_1)`<br/>`out = self.conv2(out_1.sigmoid())`<br/>`out *= self.adap(out_1).sigmoid()` | 82.66    |
| `out_1 = F.relu(self.bn2(out_1))`<br>`out = self.conv2(out_1).sigmoid()`<br>`out = self.adap(out_1) * out`                              | 84.22    |
|                                                                                                                                         |          |

#### 借鉴 NAM

1. 用了 sigmoid 乘以原矩阵
2. sigmoid 之前用了 bn
3. 还在每个通道上乘以了和为 1 的数

$$
\begin{align}
att &= norm(x) \\
att &= att \times \gamma + \delta \\
att &= att \times \frac\gamma{sum(\gamma)} \\
out &= att.sigmoid() \times x
\end{align}
$$

| 配置                                 | Accuracy |
| ------------------------------------ | -------- |
| `out = self.nam(x) + self.bn_s(out)` | 86.41    |
