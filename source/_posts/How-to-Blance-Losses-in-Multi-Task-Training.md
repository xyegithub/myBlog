---
title: How to Blance Losses in Multi Task Training
top: false
cover: false
toc: true
mathjax: true
date: 2022-01-10 14:34:19
password:
summary:
description: 在多任务模型中，如何平衡多个loss函数。
categories:
- Little Things
tags:
- Multi Task Training
---

# 论文中有的方法

Task Learning Using Uncertainty to Weigh Losses for Scene Geometry and Semantics

Multi-Task Learning as Multi-Objective Optimization

Multi-Task Learning Using Uncertainty to Weigh Losses for Scene Geometry and Semantics

Bounding Box Regression with Uncertainty for Accurate Object Detection

这些论文主要是一种不确定性的方法，从预测不确定性的角度引入Bayesian框架，根据各个loss分量当前的大小自动设定其权重。

# 利用Focal loss

Dynamic Task Prioritization for Multitask Learning

这里主要想讲的是利用focal loss的方法，比较简单。

每个task都有一个loss函数和KPI (key performance indicator)。KPI其实就是任务的准确率，kpi越高说明这个任务完成得越好。

对于每个进来的 batch，每个Task_i 有个 loss_i。每个Task i 还有个不同的 KPI: k_i。那根据 Focal loss 的定义，FL(k_i, gamma_i) = -((1 - k_i)^gamma_i) * log(k_i)。一般来说我们gamma 取 2。

最后loss = sum(FL(k_i, gamma_i) * loss_i)，loss前面乘以得这个系数FL，就是一个自适应的权重，当任务完成得很好的时候，权重就比较小，不怎么优化这个loss了，当任务完成得不好的时候，权重就会比较大。 
