---
title: AutoML Reading Note 1
date: 2018-11-25 12:22:39
tags:
    - Machine Learning
    - AutoML
    - Optimization
    - Reading Note
---

Although there are currently several developed machine learning suites,such as Keras, Pytorch, etc, facilitating the prevalence of machine learning techniques, data scientists or machine learning engineers still need to face the difficulty of hyperparameter choice of machine learning models. It has been empirically proved that for a given model, different choices of hyperparameter  also result in very different performances (accuracy, recall, etc). There is a trend of automating the hyperparameter selection for machine learning model, which is part of AutoML.

Recently I have been reading the ``ongoing'' book,  [AutoML](https://www.automl.org/book/), of which Chapter 1 introduces the existing methods to solve the hyperparameter optimization of machine learning model, and discusses several open problems as well as future research direction of this subfield. I wrote the post after reading this chapter. Note that all references can be found in the book.

<!-- more -->

# What is hyper parameter?

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.004.jpeg)

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.005.jpeg)

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.006.jpeg)

Hyperparameter optimization has led to new state-of-the-art performances for important machine learning benchmarks in several studies. Besides, Automated HPO is clearly more reproducible than manual search. It facilitates fair comparisons since dierent methods can only be compared fairly if they all receive the same level of tuning for the problem at hand.



# Problem statement of hyperparameter optimization

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.007.jpeg)

# Methods to optimize hyperparameter

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.008.jpeg)

## Black hyperparameter optimization

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.009.jpeg)

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.010.jpeg)

Bayesian optimization is actually an iterative algorithm with the probabilisitic surrogate model and acquisition function. In each iteration, the surrogate model is fitted to all observations of the target function made so far. Then, the acquisition function, which uses the predictive distribution of the probabilisitic model, determines the utility of different candidate points, trading off exploration and exploitation.

# Multi-fidelity optimization

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.011.jpeg)

# ![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.012.jpeg)

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.013.jpeg)

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.014.jpeg)

Multi-task Bayesian optimization  uses a multi-task Gaussian process to model the performance of related tasks and to automatically learn the tasks' correlation during the optimization process. This method can dynamically switch between cheaper, low-fidelity tasks and the expensive, high-fidelity target task based on a cost-aware information-theoretic acquisition function. In practice, the proposed method starts exploring the conguration space on the cheaper task and only switches to the more expensive conguration space in later parts of the optimization, approximately halving the time required for HPO.

# Applications

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.015.jpeg)

# In the future

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML1/Intro%20To%20AutoML.016.jpeg)