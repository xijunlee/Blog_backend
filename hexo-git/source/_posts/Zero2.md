---
title: AlphaGo Zero科普之从一无所知到也能吹水（下篇）
date: 2017-11-05 22:42:12
tags:
	- Reinforcement Learning
	- CNN
	- MCTS
---

## 写在前面

上篇介绍了Alpha Go的两个最主要的组件：卷积神经网络和蒙特卡洛树搜索。这篇开始进入正题，但在介绍AlphaGo Zero之前，我会先介绍其前身Alpha Go。因为这二者从本质上是一样的，只是前者在网络架构稍许不一样。我觉得先弄懂了Alpha Go之后，再看AlphaGo Zero的优化会有一种理所当然的感觉。

## Alpha Go

这里开始进入正题了，有了对蒙特卡洛树搜索和卷积神经网络的初步认识，我觉得就比较容易理解Alpha Go的本质了。本节分为三个小节，分别是策略网络(Policy Network)，价值网络(Value Network)以及与前两个网络融合的蒙特卡洛树搜索。

<!--more-->

### 监督学习下的策略网络 Supervised Learning Policy Network

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/alphagozero/WechatIMG122.png %}

这里的监督学习策略网络p_sigma就是之前提到的学习人类棋手下棋风格的卷积神经网络。Deepmind的研究人员收集了大量的职业棋手的棋谱$(\vec{s},\vec{a})$后，利用随机梯度下降方法训练出了p_sigma。该网络的输入是Deepmind按照围棋规则和他们自己的理解构造的人工特征，详见下图，共有48个特征，每个特征是$19\times 19$的矩阵。因此该网络的输入$\vec{s}$是$19\times 19\times 48$的。然后输出是一个落子概率向量$\vec{a}$，即在每个合法位置落子的概率。

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/alphagozero/WechatIMG123.png %}

虽然p_sigma能比较精确地模拟人类棋手的下期风格，但是因为网络结果比较复杂，网络每次计算时间比较长。为了能在后续的蒙特卡洛树搜索中能实现快速走子，Deepmind又训练了一个结构相对简单的快速走子网络(Rollout Network)p_pi。p_pi使用了更少的特征，结构相对p_sigma更简单，因此p_pi会算的更快但是算的没有p_sigma精确。

### 强化学习下的策略网络 Reinforcement Learning Policy Network

先稍微简单介绍下强化学习。

>强化学习是一类算法, 是让计算机实现从一开始什么都不懂, 脑袋里没有一点想法, 通过不断地尝试, 从错误中学习, 最后找到规律, 学会了达到目的的方法. 这就是一个完整的强化学习过程. 实际中的强化学习例子有很多. 比如近期最有名的 Alpha go, 机器头一次在围棋场上战胜人类高手, 让计算机自己学着玩经典游戏 Atari, 这些都是让计算机在不断的尝试中更新自己的行为准则, 从而一步步学会如何下好围棋, 如何操控游戏得到高分. 既然要让计算机自己学, 那计算机通过什么来学习呢?原来计算机也需要一位虚拟的老师, 这个老师比较吝啬, 他不会告诉你如何移动, 如何做决定, 他为你做的事只有给你的行为打分, 那我们应该以什么形式学习这些现有的资源, 或者说怎么样只从分数中学习到我应该怎样做决定呢? 很简单, 我只需要记住那些高分, 低分对应的行为, 下次用同样的行为拿高分, 并避免低分的行为.比如老师会根据我的开心程度来打分, 我开心时, 可以得到高分, 我不开心时得到低分. 有了这些被打分的经验, 我就能判断为了拿到高分, 我应该选择一张开心的脸, 避免选到伤心的脸. 这也是强化学习的核心思想. 可以看出在强化学习中, 一种行为的分数是十分重要的. 所以强化学习具有分数导向性. 我们换一个角度来思考.这种分数导向性好比我们在监督学习中的正确标签.

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/alphagozero/WechatIMG124.png %}

这样子大家应该对强化学习有一个感性的认知了。AlphaGo在构造好了监督学习下的策略网络后，就开始利用强化学习来提升策略网络的棋力了，记这个强化学习的策略网络为p_rho。p_rho在输入、输出和网络结构上与p_sigma可谓是完全一样。初始化时,p_rho的参数就等于p_sigma。之后该网络p_rho利用自我博弈和策略梯度强化学更新其参数，以提升其棋力。实验证明p_rho的棋力确实是要高于p_sigma的。这意味着强化学习网络青出于蓝而胜于蓝；这意味着强化学习网络从人类老师那里学到了围棋知识后，通过自我对弈学到了更多的围棋知识。

### 价值网络 Value Network

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/alphagozero/WechatIMG125.png %}

Alpha Go中还构造了一个网络，即价值网络v。该网络的用途是给定一个棋局局面$\vec{s}$，立马根据$\vec{s}$输出我赢棋的概率$v$。

价值网络v的训练方法依旧是强化学习。更细一点说明如下：

1.针对棋局局面$\vec{s}$，不断利用强化策略网络p_rho进行自我对弈一直下棋到最后，就会得到一个输赢结果$z=+1$或者$z=-1$。其中1表示黑子赢棋，-1表示白子赢棋；
2.不断执行上一步骤，就能得到一系列的$(\vec{s},z)$对了。将这些$(\vec{s},z)$对作为训练数据来训练价值网络v。

### “融合”的蒙特卡洛树搜索

训练好了上述所有网络后，我们现在就已经万事具备了。上述的这些网络都是需要提前训练好的，这个训练过程的时间是非常漫长的，但是训练好之后就可以立马使用。不过，如果只单纯用策略网络去跟人下棋的话，一般是会被人类玩家虐的，因为这样的策略还是太简单了。所以，需要将上述网络与蒙特卡洛树搜索进行结合。下图正是Alpha Go中将策略网络和价值网络融合到蒙特卡洛树搜索的架构与算法流程：

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/alphagozero/WechatIMG126.png %}

首先，认识一下这个树结构。

树中的节点表示棋局中的一个局面$\vec{s}$，每个点的状态可以使用价值网络v进行评判其赢棋概率$v$。

每条边$(\vec{s},\vec{a})$表示基于某个局面$\vec{s}$的落子决策，每条边还存储三个状态$P(\vec{s},\vec{a})$，$N(\vec{s},\vec{a})$，$Q(\vec{s},\vec{a})$。$P(\vec{s},\vec{a})$是由策略网络p_sigma得到，表示基于当前状态$\vec{s}$下，采取策略$\vec{a}$的概率。$N(\vec{s},\vec{a})$表示经过边$(\vec{s},\vec{a})$的次数。$Q(\vec{s},\vec{a})$表示策略值，计算公式见上图。

Alpha Go Zero中的树搜索步骤可以总结为：

1.这个过程的第一步叫选择（Selection）。从根节点往下走，每次都选一个“最值得看的子节点”，直到来到一个“存在未扩展的子节点”的节点。什么叫做“存在未扩展的子节点”，其实就是指这个局面存在未走过的后续着法。选择的依据如上图公式所示。
2.第二步叫扩展（Expansion）。对应之前所说的“未扩展的子节点”，就是还没有试过的一个着法。我们利用策略网络p_sigma对其进行扩展，即基于当前局面下给出其所有可能落子位置的概率。
3.第三步是模拟（Simluation）。从上面这个没有试过的着法开始，用快速走子策略（Rollout policy）走到底，得到一个胜负结果$z_L$。另一方面，我们还可以利用价值网络v对当前这个局面进行评估，得到赢棋概率$v$。然后将这两个结果进行加权平均，得到一个更加客观的赢棋概率，记为$V$。
4.第四步是回溯（Backpropagation）。把模拟的结果更新回它的所有父节点上。这里需要更新的参数有$N(\vec{s},\vec{a})$，$Q(\vec{s},\vec{a})$。

好了，以上就是Alpha Go原理比较完整的概述了，如果大家对细节很感兴趣的话，还是阅读原文最好。

## AlphaGo Zero

### AlphaGo Zero与Alpha Go的不同之处

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/alphagozero/WechatIMG128.png %}

根据我的理解，Zero和之前的Alpha Go的主要差异有三点:

1.Zero采用了新的网络架构。将Alpha Go中的策略网络和价值网络融合成为一个网络，我认为这个想法很自然，因为策略网络和价值网络的输入层和网络隐层的结构完全一样，只不过前者输出落子概率向量，后者输出当前局面的赢棋概率。
2.Zero采用了目前最先进的卷积神经网络——深度残差网络(Deep Residual Network)，具体参见[论文](https://arxiv.org/abs/1512.03385)。残差网络的出现一定程度地缓解了卷积神经网络中的梯度消失的问题，从而使得网络突破深度的瓶颈。换句话说，就是Zero采取的网络升级了，比Alpha Go有更强的学习能力。
3.Zero直接用棋盘状态的原始模样作为训练样本。Alpha Go使用了人工构造的特征作为训练样本输入网络，而Zero则是直接用棋盘状态的原始模样作为训练样本输入网络。

### AlphaGo Zero中的强化学习

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/alphagozero/WechatIMG127.png %}

Zero将Alpha Go中的策略网络和价值网络融合成为一个网络$f_\theta$。该网络的输入是棋盘当前状态以及前七步状态$\vec{s}$，输出是落子概率向量$\vec{a}$和赢棋概率$v$。

该网络的参数$\theta$是随机初始化的，也就是说没有学习人类知识，完全从零开始，这也是AlphaGo Zero中Zero取名的由来。而参数$\theta$的训练是利用在$f_\theta$指导下的蒙特卡洛树搜索的强化学习完成的。具体的步骤如下:

1.自我博弈(Self-play)。蒙特卡洛树搜索在$f_\theta$的指导下进行自我博弈，直到棋局结束。在这个自我博弈过程中，会得到一系列的棋盘状态$\vec{s}_i$，对应的落子概率向量$\vec{\pi}_i$，以及一个最后的赢棋结果$z$。

2.神经网络训练。利用第一步得到的一系列（棋盘状态，落子概率向量，赢棋结果）的来更新网络参数$\theta$。更新后的网络$f_\theta$又去指导蒙特卡洛树搜索进行自我博弈。

自我博弈和网络训练交替进行，直到网络参数$\theta$收敛为止。这样，我们就能得到一个无比强大的融合网络$f_\theta$。

### AlphaGo Zero中的蒙特卡洛树搜索

Zero中的树搜索和Alpha Go中的树搜索在流程和结构上可以说是一模一样，如下图所示，所以就不再赘述了。差异是Zero用了一个融合过后的网络$f_\theta$，这个网络干了Alpha Go中的策略网络和价值网络的活。

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/alphagozero/WechatIMG130.png %}

## 小结

终于把Alpha Go与AlphaGo Zero系统地梳理了一遍了。不得不感慨Alpha Go以及AlphaGo Zero是科研与工程的极致产物，但也要认识到AlphaGo Zero并没有像媒体吹地那么神，其与真正的智能还是差的很远。最后，我就节选贴上田渊栋最近对于Zero的[评论](https://zhuanlan.zhihu.com/p/30750293?utm_source=wechat_session&utm_medium=social)，其写得非常到位和客观。

>AlphaGo厉害的地方在于结合了工程和科研两方面的工作，通过大量计算资源和工程优化将一个方向推向了极致，并且同时借鉴了十年来大家在围棋上及在计算机视觉上的点滴进展，围棋和强化学习方向上像蒙特卡罗树搜索，自对弈（俗称“左右互搏”），随机走子盘面估值，用人工特征加浅层网络进行快速走子，权衡广度和深度搜索，权衡从头探索和先验知识，计算机视觉方向上像卷积神经网络（CNN），残差网络（ResNet），旋转翻折样本增强，等等。这些都不是DeepMind团队率先想出来的，而是过去的经验一点点积累起来得到的。只是过去的点滴进步并没有进入公众的视野，而AlphaGo达成了这最后的一步。AlphaGo在业内的影响力并不像在大众中（尤其是国内）那么大。在人工智能领域，每个方法都有优缺点和局限性，没有一种方法是万能的。让在AlphaGo上做出惊人结果的卷积神经网络，早先在计算机视觉上已获得巨大的成功，但让它去拟合含有大量离散特征的广告数据，大家都知道效果不佳；同样左右互搏对其它游戏也有相当不错的效果，但让它去做优化机器翻译，未必有人工标定的数据好。我想主要的问题在于我们之前把围棋这个千年的文化传承神化了，所以现在才有这样大的反弹；若是现在再把AlphaGo神化，那不免又掉进一模一样的圈子里去了。国外对于AlphaGo相比之下要理性很多，在各大论坛上也有更多技术上的深入探讨，而不是陷入玄之又玄的空谈。等到大家有这个实力AlphaGo拆解得七零八落，对它的每个部分都有比较清晰的了解的时候，那我相信它的神秘感，和现在这些疑惑和担扰，也就烟消云散。            --- 田渊栋，Facebook人工智能组研究员

---
Reference
https://storage.googleapis.com/deepmind-media/alphago/AlphaGoNaturePaper.pdf
http://www.nature.com/nature/journal/v550/n7676/full/nature24270.html
https://charlesliuyx.github.io/2017/10/18/深入浅出看懂AlphaGo元/
https://www.zhihu.com/question/39916945/answer/184152952
http://ieeexplore.ieee.org/document/6145622/
https://zhuanlan.zhihu.com/p/25345778
https://arxiv.org/abs/1412.6564
https://morvanzhou.github.io/tutorials/machine-learning/reinforcement-learning/1-1-A-RL/
https://arxiv.org/abs/1512.03385
https://zhuanlan.zhihu.com/p/30750293?utm_source=wechat_session&utm_medium=social