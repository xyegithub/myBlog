---
title: The First Step to GAN
top: false
cover: false
toc: true
date: 2022-05-13 10:17:22
password:
summary:
description: The first step for learning GAN
categories:
  - Machine Learning
  - Generative Adversarial Network
tags:
  - Generative Adversarial Network
---

# The Reason For GAN

GAN is a generative model, which can only create data, but not directly provides
the density function of data. So why we need it. There are several reasons.

- Generate samples of real world
- GANs training process does not depend on the maximum likelihood estimation.
- GAN does not "see" the training data during the training process. Thus it
  rarely suffers from the over-fitting issue.
- GAN is good at capturing the distribution of data.

# The thought of GAN from Game Theory

The adversarial process of GAN is related to Max-Min game in Game Theory.

The balance point of the generative model and discriminate model python
