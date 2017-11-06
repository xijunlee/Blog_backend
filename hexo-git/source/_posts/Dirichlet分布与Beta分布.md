---
title: 通俗理解Dirichlet分布及其实践
date: 2017-09-09 21:17:26
tags:
	Probability Theory
---

## 写在前面

最近项目中有一部分用到了Dirichlet分布及其采样，这个分布应该是本科阶段的概率论没有教过的吧（也可能是我上课走神😓）。于是，从头学习，整理如下。

## Beta分布

要说Dirichlet分布之前，先了解下其特殊情况Beta分布。一言以蔽之，Beta分布是二项分布的分布。二项分布中的参数theta在Beta分布中，也被认为是一个随机变量，因此Beta分布是用来描述这个随机变量theta的。下面用知乎上一个通俗的例子来说明（我觉得这个例子讲得相当好）。更严谨的叙述参看[PRML中相关章节](https://mqshen.gitbooks.io/prml/Chapter2/binary/beta_distribute.html)。

<!--more-->

用一句话来说，beta分布可以看作一个概率的概率分布，当你不知道一个东西的具体概率是多少时，它可以给出了所有概率出现的可能性大小。

举一个简单的例子，熟悉棒球运动的都知道有一个指标就是棒球击球率(batting average)，就是用一个运动员击中的球数除以击球的总数，我们一般认为0.266是正常水平的击球率，而如果击球率高达0.3就被认为是非常优秀的。现在有一个棒球运动员，我们希望能够预测他在这一赛季中的棒球击球率是多少。你可能就会直接计算棒球击球率，用击中的数除以击球数，但是如果这个棒球运动员只打了一次，而且还命中了，那么他就击球率就是100%了，这显然是不合理的，因为根据棒球的历史信息，我们知道这个击球率应该是0.215到0.36之间才对啊。对于这个问题，我们可以用一个二项分布表示（一系列成功或失败），一个最好的方法来表示这些经验（在统计中称为先验信息）就是用beta分布，这表示在我们没有看到这个运动员打球之前，我们就有了一个大概的范围。beta分布的定义域是(0,1)这就跟概率的范围是一样的。接下来我们将这些先验信息转换为beta分布的参数，我们知道一个击球率应该是平均0.27左右，而他的范围是0.21到0.35，那么根据这个信息，我们可以取α=81,β=219。

{% img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/dirichlet/1.png %}

这是因为：

1. beta分布的均值是$$\frac{\alpha}{\alpha+\beta}=\frac{81}{81+219}=0.27$$
2. 从上图中，我们可以看到该分布主要落在(0.2,0.35)间，这就是从先验中得到的知识。

在这个例子里，我们的x轴就表示各个击球率的取值，x对应的y值就是这个击球率所对应的概率。也就是说beta分布可以看作一个**概率的概率分布**。那么有了先验信息后，现在我们考虑一个运动员只打一次球，那么他现在的数据就是”1中;1击”。这时候我们就可以**更新我们的分布**了，让这个曲线**做一些移动去适应我们的新信息**。beta分布在数学上就给我们提供了这一性质，他与二项分布是**共轭先验**的（Conjugate_prior）。所谓共轭先验就是先验分布是beta分布，而后验分布同样是beta分布。结果很简单：

$$ \mbox{Beta}(\alpha_0+\mbox{hits}, \beta_0+\mbox{misses})$$
其中α0和β0是一开始的参数，在这里是81和219。所以在这一例子里，α增加了1(击中了一次)。β没有增加(没有漏球)。这就是我们的新的beta分布Beta(81+1,219)，我们跟原来的比较一下：

{% img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/dirichlet/2.png %}

可以看到这个分布其实没多大变化，这是因为只打了1次球并不能说明什么问题。但是如果我们得到了更多的数据，假设一共打了300次，其中击中了100次，200次没击中，那么这一新分布就是：

$$\mbox{Beta}(81+100, 219+200)$$

{% img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/dirichlet/3.png %}

注意到这个曲线变得更加尖，并且平移到了一个右边的位置，表示比平均水平要高。

一个有趣的事情是，根据这个新的beta分布，我们可以得出他的数学期望为：

$$\frac{\alpha}{\alpha+\beta}=\frac{81+100}{81+100+219+200}=.303$$

这一结果要比直接的估计要小：

$$\frac{100}{100+200}=.333$$

你可能已经意识到，我们事实上就是在这个运动员在击球之前可以理解为他已经成功了81次，失败了219次这样一个**先验信息**。

因此，对于一个我们不知道概率theta是什么的二项分布，而又有一些合理的猜测时，beta分布能很好的作为描述该概率theta的概率分布。

**结论1**：二项分布与Beta分布是**共轭先验**的。即Beta分布乘上一个二项分布的似然函数后，得到的后验分布仍然是一个Beta分布。

关于其的证明，可以参考reference中的[链接](https://www.zhihu.com/question/30269898)。

## Dirichlet分布

先给出一个结论:

**结论2**：Dirichlet分布就是由2种结果bernoulli trial导出的Beta分布外推到k种的一般情形。

详细一点地说，二项分布的分布是Beta分布，而二项分布的推广形式多项分布的分布就是Dirichlet分布。

**结论3**：Dirichlet分布与多项分布是共轭先验的。即Dirichlet分布乘上一个多项分布的似然函数后，得到的后验分布仍然是一个Dirichlet分布。

严谨的叙述参考[PRML相关章节](https://mqshen.gitbooks.io/prml/Chapter2/multinomial/dirichlet_distribute.html)。

## 实践：Dirichlet分布的估计

即在给定多项分布中概率向量p的一些样本后，如何估计Dirichlet分布中的参数alpha。关于这个问题，Microsoft Research的[Tom Minka](https://tminka.github.io)给出了两种解决办法。无外乎都是极大似然法，两种方法的差异在于第一种方法是采用拟牛顿法来迭代求最大似然估计，而第二种方法则是稍微曲线救国了一下，先定义两个与alpha相关的值，通过先求这两个值从而求得alpha的最大似然估计，其中采用了EM的思想。具体细节可以参考其[论文](https://tminka.github.io/papers/dirichlet/minka-dirichlet.pdf)。

Minka大神同时也实现了其提出的两种方法的[Matlab源码](https://github.com/tminka/fastfit)，而[Eric J. Suh](http://www.ericsuh.com)将这些算法又用Python实现了一遍，做成了一个独立的[Python Package](https://github.com/ericsuh/dirichlet)。我项目中就是直接用的这个，跪谢两位大神Orz!

---
Reference
https://github.com/tminka/fastfit
https://tminka.github.io/papers/dirichlet/minka-dirichlet.pdf
https://www.zhihu.com/question/30269898
https://mqshen.gitbooks.io/prml/Chapter2/multinomial/dirichlet_distribute.html
https://mqshen.gitbooks.io/prml/Chapter2/binary/beta_distribute.html




