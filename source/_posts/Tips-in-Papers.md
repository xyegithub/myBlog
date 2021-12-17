---
title: Tips in Papers
top: false
cover: false
toc: true
mathjax: true
date: 2021-12-14 16:33:03
password:
summary:
description: 读过的论文记录，记录读过论文里面，新获取的信息。
categories:
- About Papers
tags:
- Papers
---

# Hard Attention

## 2019 Scacader: Improving Accuracy of Hard Attention Models for Vision

### Hard Attention选择相关的特征作为输入，是真正具有可解释性的。但是Soft Attention不具有可解释性，小权重的特征并不一定不重要

Typical soft attention mechanisms rescale features at one or more stages of the network. The soft mask used for rescaling often to provide some insight into the model's decision-making process, but the model's final decision may nonetheless rely on information provided by features with small weights [2018, Learn to pay attention.].

### 文章提出了Hard Attention与双阶段目标检测算法的相似性。他们都是截取图像块作为输入，然后进行分类。不同的是，目标检测算法对于目标的位置是有标签的，而Hard Attention是无标签的。

Altough our aim in this work is to perform classification with only image-level class labels, out approach bears some resembalance to two-stage object detection models. 

These models operate by generating many region proposals and then applying a classification model to each proposal. 

Unlike our work, these approaches use ground-truth bounding boxes to train the classification model, and modern architectures also use bounding boxes to supervise the proposal generator.

**目标检测和Hard Attention的相似之处在于，他们都同时关注目标的位置和类别。相比于目标检测，Hard Attention可以做的更精细, i.e., 它可以像目标检测一样在图像域上挑选特征，它还可以在任意一个特征域里挑选特征；它不仅可以像目标检测一样，挑选空域的特征，还可以挑选通道域的特征。**

# Regularization 

## ADCM: Attentnion Dropout Convolutional Module

![ADCM](ADCM.jpg)

在CBAM的基础上加入了正则化，把CBAM产生的attention weights作为Drop的概率引导，来对feature map进行drop。是一种对attention机制的正则化方法，很容以把它误解为hard attention。
