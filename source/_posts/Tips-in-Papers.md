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
- Personal Thought
---

# Hard Attention

## 2019 Scacader: Improving Accuracy of Hard Attention Models for Vision

### Hard Attention选择相关的特征作为输入，是真正具有可解释性的。但是Soft Attention不具有可解释性，小权重的特征并不一定不重要

Typical soft attention mechanisms rescale features at one or more stages of the network. The soft mask used for rescaling often to provide some insight into the model's decision-making process, but the model's final decision may nonetheless rely on information provided by features with small weights [^1]

[^1]:2018,Learn to pay attention.





### 文章提出了Hard Attention与双阶段目标检测算法的相似性。他们都是截取图像块作为输入，然后进行分类。不同的是，目标检测算法对于目标的位置是有标签的，而Hard Attention是无标签的。

Altough our aim in this work is to perform classification with only image-level class labels, out approach bears some resembalance to two-stage object detection models. 

These models operate by generating many region proposals and then applying a classification model to each proposal. 

Unlike our work, these approaches use ground-truth bounding boxes to train the classification model, and modern architectures also use bounding boxes to supervise the proposal generator.

**目标检测和Hard Attention的相似之处在于，他们都同时关注目标的位置和类别。相比于目标检测，Hard Attention可以做的更精细, i.e., 它可以像目标检测一样在图像域上挑选特征，它还可以在任意一个特征域里挑选特征；它不仅可以像目标检测一样，挑选空域的特征，还可以挑选通道域的特征。**

### 总结构

![](Saccader_Over.jpg)

最上面的rep. net以及logits per location之前都属于representation network。这部分挺常规，但是使用了‘BagNet’[^2]的方法，该方法保持了输出特征图中每个像素的感受野大小。

下面的atten. net就是几个卷积层的堆叠。没有attention机制。到Sacadder cell之前都是常规操作，除了一个what和where的concat得到mixed。

Saccader cell是技术关键点。

**coordinate at time t的slice操作，对于坐标的选择而言，是一个不可导的操作。这里是强化学习介入的地方。**

[^2]:2019, Approximating CNNs with bag-of-local-features models works surprisingly well on ImageNet.  

### Saccader cell

![](Saccader_Cell.jpg)

值得注意的几点

1. Cell state是一个state的序列，每个state都是一个经过2d softmax的logit。这个logit表示该state预测的位置。

2. 需要保证cell state中预测之间都是不同的。$C^t$记录了t时刻位置探索过的所有位置。那些位置的值是1。所以$C^{t - 1}$两次介入$C^t$的计算都乘以一个非常小的数$-10^5$ ，这样就保证了在2d softmax的时候，探索过的位置无法胜出。

   > The cell includes a 2D state ($C^t$) that keeps memory of the visited locations until time t by placing 1 in the corresponding location in the cell state. We use this state to prevent the network from returning to previously seen locations.

3. 在制作$C^t$的过程中，信息来源有两个mixed feature和$C^{t - 1}$。最后得到的$C^t$通道是1，所以mixed feature空间维度的压缩是必然的。在压缩的时候，选用了channel attention机制。channel attention机制又需要先空间压缩，这里不像SE-Net一样直接压缩空间，而是又做了一个空间的mask 压缩空间，这个mask用了$C^{t - 1}$的信息，去除掉了已经探索的位置信息。

**Saccader Cell的关键就在于产生一系列的state。这些state可以用强化学习算法优化，使得state可以预测物体的位置，从而就进行了feature的选择。**

### 训练策略

> The goal of our training is to learn a policy that predicts a sequence of visual attentnion locations that is useful to the downsteam task (here image classification) in absence of location labels.
>
> We performed a three step training procedure using only the training class lables as supervision.

![](Saccader_eq1.jpg)

1. 预训练了representation network

![](Saccader_eq2.jpg)

2. 训练了location network (attention network, $1 \times 1$ mixing convolution and Sacader cell)

![](Saccader_eq3.jpg)

3. > we trained the whole model to maximize the expected reward, where the reward ($r \in \{0, 1\}$) represents whether the model final prediction after 6 glimpses (T = 6) is correct. 





# Regularization 

## ADCM: Attentnion Dropout Convolutional Module

![ADCM](ADCM.jpg)

在CBAM的基础上加入了正则化，把CBAM产生的attention weights作为Drop的概率引导，来对feature map进行drop。是一种对attention机制的正则化方法，很容以把它误解为hard attention。
