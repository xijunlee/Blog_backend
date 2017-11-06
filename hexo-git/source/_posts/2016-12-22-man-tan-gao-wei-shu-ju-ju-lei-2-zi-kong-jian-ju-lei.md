---
layout: post
title: "漫谈高维数据聚类(2):子空间聚类"
date: 2016-12-22 09:46:37 +0800
comments: true
tags:
    - Clustering
    - Machine Learning

    
---
## 摘要

如今处理高维数据的问题往往会有两座“大山”，分别是存储成本高和计算时间过长。显然，这是因为数据的高维数造成的。面对大规模的高维数据的聚类任务，传统的聚类算法有点hold不住了，正因此，子空间分割算法应运而生。

前面的一篇文章应该是解释清楚了啥是聚类，也安利了一个经典的聚类算法——K均值聚类。本篇开始正式介绍一些子空间聚类算法，以及这些聚类算法的应用实例。

<!--more-->

## 1.什么是子空间聚类
> 给定一个n个样本构成的矩阵$X=[x_1, x_2, ..., x_n]\in R^{m*n},x_i \in  R^m$，并且已知这n个样本是分别来自k个子空间$\{S_i\},i=1, ..., k$。令$\{d_i<m,i=1,...,k\}$表示k个子空间的维度（其中$d_i$是未知的）。子空间聚类的目的是将这个n个样本正确地划归到各自所属的子空间中去，即将n个样本聚成k类，每一个类就是一个子空间。

{% img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/C83B7ACE-76BC-4D9C-A24B-DFA41C1244D1.png %}

从上图可以看到，总共有三个子空间，每个子空间上都有一些样本，位于同一个子空间内的样本就可以说是同一类的。每一个子空间有其相应的维数d,和一组基$[b_1,b_2,…,b_d]$，由这组基可以表示该子空间中任意一个向量。

## 2.稀疏表示模型和低秩表示模型
目前，有四大类主流的求解子空间聚类问题的算法，分别是：

（1）基于统计的方法：混合数据假设是从服从某一概率分布（如混合高斯分布）中抽取出的独立样本集，于是数据的分割问题就转化为一模型估计问题。代表性的工作有凝聚有损压缩[2]和随机抽样一致[1]；

（2）基于矩阵分解的方法：将数据矩阵分解为一正交基矩阵和一低秩矩阵的乘积，从分解结果的结构来揭示聚类的特性。当子空间含有噪声和奇异值，或者独立子空间的假设不成立时，此类方法的效果不尽人意。代表性的工作有K子空间分割[4]；

（3）基于代数的方法：可以处理子空间不是相互独立的情况，但计算量大，且对噪声和奇异值敏感。代表性的工作有Generalized PCA(GPCA)[3]；

（4）基于谱聚类的方法：谱聚类算法是一种对高维数据进行聚类的技术。基于谱聚类的子空间分割算法先根据观测样本求得一个相似矩阵，然后对这个相似矩阵进行谱聚类获得最终的聚类结果。代表性的工作有稀疏子空间聚类[5]和低秩表示子空间聚类[6][7]。

而我要安利的是基于谱聚类的两个算法：稀疏表示子空间聚类算法和低秩表示子空间聚类算法，这两个算法都是比较简单和直观的，是子空间聚类研究这一块的基石性的算法，很多新算法都是在这两个算法的基础上加以改进而提出来的。当然，想要弄清楚这两个算法，还是需要一些简单的线性代数知识的。

### 稀疏表示(Sparse Representation)

稀疏表示这一概念的提出，说到底还是受到压缩感知理论[8][9]的启发。该理论认为很多高维数据是冗余的，如果其具有可压缩性，那么可以只需要通过少量的采样便可恢复原始高维数据。更简单地说就是，许多高维数据是存在其低维表示的。

学过线性代数的都应该知道线性相关这一概念，即向量组$X=[x_1,x_2,…,x_n]$,其中$x_i$是$X$的列向量，若存在不全为0 的系数$a_1,a_2,…,a_n$，使得$a_1*x_1+a_2*x_2+…+a_n*x_n=0$成立，则说$x_1,x_2,…,x_n$是线性相关的，如若$a_i$不等于零，那$x_i$就可以被其余向量线性表示，即$x_i=-(a_1*x_1+a_2*x_2+…a_{i-1}*x_{i-1}+a_{i+1}*x_{i+1}+…+a_n*x_n)/a_i$。

有了以上两个认知，就可以理解稀疏表示了。在前面提到过位于同一个子空间中的样本，如果样本数足够多，那么某一个样本$x_i$是可以被与它位于同一个子空间中的其他样本线性表示的，而我们希望用尽量少的样本来表示$x_i$$，这就是稀疏表示的简单理解，用数学语言描述该模型如下：

给定一个n个样本构成的矩阵$X=[x_1,x_2,...,x_n]\in R^{m*n},x_i\in R^m$,其中每一列是一个样本，由$X$中d个样本构成一个“字典”$D=[x_{i1},x_{i2},...,x_{id}]\in R^{m*d}$,$D$中每一列成为原子。那么，每一个样本都可以表示成“字典”中原子的线性组合：

$$X=DZ$$

其中,$Z=[z_1,z_2,...,z_n]$是系数矩阵，$z_i$是样本$x_i$的“表示”。而字典$D$通常是过完备的，因为经常是选取全部样本作为字典，即$d=n$。因此，上市会有多个解。但是如给系数矩阵加上约束，则会有唯一解。稀疏表示即是要求系数矩阵$Z$是最稀疏的，即

$$\min \Arrowvert Z \Arrowvert_1$$

$$s.t.\quad X=DZ$$

其中，$\Arrowvert \cdot\Arrowvert_1$是求矩阵所有元素的绝对值的和。进一步，这个模型还可以加上噪声，即

$$\min \Arrowvert Z \Arrowvert_1+\lambda\Arrowvert E\Arrowvert_F$$

$$s.t.\quad X=DZ+E$$

### 低秩表示(Low-rank Representation)

低秩表示模型和稀疏表示模型几乎一样，区别仅在于对系数矩阵的约束不同，在低秩表示中，它期望系数表示矩阵Z尽可能的低秩，用数学语言描述如下:

$$ \min rank(Z)$$

$$s.t.\quad X=DZ$$

其中,$rank(Z)$表示矩阵$Z$的秩。与$\ell _0$范数一样，秩函数也具有离散组合性质，因此求解上式是一个NP难得。但是如果上式存在一个较低秩的解的话，秩优化问题可以被松弛为核范数最小化问题，即:

$$\min \Arrowvert Z \Arrowvert_*$$

$$s.t.\quad X=DZ$$

其中，$\Arrowvert \cdot \Arrowvert_*$是矩阵的核范数。松弛的原理可以这么理解，一个矩阵的秩等于其非零奇异值的个数。那么矩阵的秩最小化等价于矩阵的奇异值非零个数尽量少，进一步可以松弛为矩阵的所有奇异值的和尽量小。

低秩表示模型是在稀疏表示模型之后提出来的，当然它比稀疏表示模型的性能更好，这是因为低秩表示模型中的核函数自带聚集属性，具体的原因我推荐论文[10]中的讲解（在其第五章中）。

##上述两个模型的求解

求解这两个模型的方法有很多，有兴趣的朋友可以参阅论文[11]。


---
Reference
[1]Fischler M., Bolles R. RANSAC random sample consensus: a paradigm for model fitting with applications to image analysis and automated cartography. Journal of ACM, 1981, 24(6): 381–395.
[2]Ma Y., Derksen H., Hong W., Wright J. Segmentation of multivariate mixed data via lossy coding and compression. IEEE Trans. Pattern Analysis and Machine Intelligence, 2007, 29(9): 1546–1562.
[3]Vidal R., Ma Y., Sastry S. Generalized principal component analysis (GPCA). IEEE Trans. Pattern Analysis and Machine Intelligence, 2005, 27(12): 1–15.
[4]Lu L., Vidal R. Combined central and subspace clustering on computer vision applications. In: Proc. 23rd Int’l Conf. Machine Learning (ICML), 2006, pp.593–600.
[5] Elhamifar E, Vidal R. Sparse subspace clustering[J]. IEEE Conference on Computer Vision and Pattern Recognition, Cvpr, 2009:2790 - 2797.
[6]G L, Z L, S Y, et al. Robust Recovery of Subspace Structures by LowRank Representation[J]. Pattern Analysis & Machine Intelligence IEEE Transactions on, 2010, 35(1):171 - 184.
[7]X G, K Z, D T, et al. Image SuperResolution With Sparse Neighbor Embedding[J]. IEEE Transactions on Image Processing, 2012, 21(7):3194 - 3205.
[8]Donoho D L． Compressed sensing． IEEE Transactions on Information Theory,2006 52(4)：1289-1306．
[9]Cand6s E．Compressive sampling．Proceedings of Proceedings of Inter-national Congress of Mathematicians，2006．1433-1452．
[10] 卢参义. 基于稀疏表示的人脸分类与聚类[D]. 中国科学技术大学, 2012. DOI:10.7666/d.y2126052.
[11]Lin Z, Chen M, Ma Y. The Augmented Lagrange Multiplier Method for Exact Recovery of Corrupted Low-Rank Matrices[J]. Eprint Arxiv, 2010.
