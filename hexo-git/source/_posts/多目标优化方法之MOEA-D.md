---
title: 多目标优化方法之MOEA-D
date: 2017-08-29 22:57:35
tags: 
	Multiobjective Optimization
---

## 写在前面

最近项目有一部分是在做多目标优化，调研了一些论文，总结一下其中目前state-of-the-art的solution —— [MOEA/D: A Multiobjective Evolutionary Algorithm Based on Decomposition](http://ieeexplore.ieee.org/document/4358754/)，这篇paper出自两位华人之手Qingfu Zhang和Hui Li。

---

下面用我组会slides上的两张图简单介绍问题和这篇paper提出的解决思路。

{% img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/MOEAD/MOEAD%20summary.png %}

<!--more-->

对于多目标优化问题F，MOEA/D的解决思路非常之简单，就是通过权重向量lamda将多个单优化目标组合到一起，将该问题转为单目标优化问题。乍一看，非常简单呢。但是其通过权重向量lambda为遗传算法中的候选解定义了邻居的概念，通过与邻居共同进化的思想，寻找pareto最优。但我觉得其缺点还是需要人为地去设置权重向量们，权重向量设置的好坏将决定最后解的质量。以下是具体算法的一个简单流程图：

{% img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/MOEAD/MOEAD%20flow%20chart.png %}
