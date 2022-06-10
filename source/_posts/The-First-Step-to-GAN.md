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
  - Methods
  - Generative Adversarial Network
tags:
  - Generative Adversarial Network
---

# Some Characteristics

- Generate samples of real world
- GANs training process does not depend on the maximum likelihood estimation.
- GAN does not "see" the training data during the training process. Thus it
  rarely suffers from the over-fitting issue.
- GAN is good at capturing the distribution of data.
- GAN does not need labeled data.
- Meaningful latent vectors.

# The thought of GAN from Game Theory

The adversarial process of GAN is related to Max-Min game in Game Theory.

The balance point of the generative model and discriminate model python

# Model Collapse

Usually you want GAN to produce a wide variety of outputs. You want, for
example, a different face for every random input to your face generator.

However, if a generator produces an especially plausible output, the generator
may learn to produce only that output. In fact, the generator is always trying
to find the one output that seems most plausible to the discriminator.

Each iteration of generator over-optimizes for a particular discriminator, and
the discriminator never manages to lean its way out of the trap. As a result the
generators rotate through a small set of output types. This form of GAN failure
is called **model collapse**.

GAN has a strong relationship with image super-resolution

# Apache Spark

Unified engine for large-scale data analytics. Apache spark is a multi-language
engine for executing data engineering data science, and machine learning on
single-node machines or clusters.

# BigDL

BigDL is a distributed deep learning library for Apache Spark, with BigDL users
can write their deep learning applications as standard Spark programs, which can
directly run on top of existing Spark or Hadoop clusters.
