---
title: reddit上有趣的SVM图解
date: 2017-03-13 13:26:10
tags: Support Vector Machine
---

## 写在前面

这是目前看到过的对SVM的最直观的解释，原题目名还叫作[Please explain Support Vector Machines (SVM) like I am a 5 year old](https://www.reddit.com/r/MachineLearning/comments/15zrpp/please_explain_support_vector_machines_svm_like_i/)。于是，稍微整理下放到博客里。我个人认为，它对SVM中`kernel function`解释地相当形象。

## 正文

Found [this](https://www.reddit.com/r/MachineLearning/comments/15zrpp/please_explain_support_vector_machines_svm_like_i/) on Reddit [r/machinelearning](https://www.reddit.com/r/MachineLearning/)

(In related news, there’s a machine learning subreddit. Wow.)

<!-- more -->

[Support Vector Machines](https://en.wikipedia.org/wiki/Support_vector_machine) (warning: Wikipedia dense article alert in previous link!) are learning models used for classification: which individuals in a population belong where? So… how do SVM and the mysterious “kernel” work?

The user curious_thoughts asked for an explanation of SVMs like s/he was a five year old. User copperking stepped up to the plate:

We have 2 colors of balls on the table that we want to separate.

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/svm_reddit/svm1.png %}

We get a stick and put it on the table, this works pretty well right?

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/svm_reddit/svm2.png %}

Some villain comes and places more balls on the table, it kind of works but one of the balls is on the wrong side and there is probably a better place to put the stick now.

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/svm_reddit/svm3.png %}

SVMs try to put the stick in the best possible place by having as big a gap on either side of the stick as possible.

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/svm_reddit/svm4.png %}

Now when the villain returns the stick is still in a pretty good spot.

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/svm_reddit/svm5.png %}

There is another trick in the SVM toolbox that is even more important. Say the villain has seen how good you are with a stick so he gives you a new challenge.

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/svm_reddit/svm6.png %}

There’s no stick in the world that will let you split those balls well, so what do you do? You flip the table of course! Throwing the balls into the air. Then, with your pro ninja skills, you grab a sheet of paper and slip it between the balls.

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/svm_reddit/svm6.png %}

Now, looking at the balls from where the villain is standing, they balls will look split by some curvy line.

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/svm_reddit/svm7.png %}

Boring adults the call balls data, the stick a classifier, the biggest gap trick optimization, call flipping the table kernelling and the piece of paper a hyperplane.


