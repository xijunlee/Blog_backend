---
title: AutoML Reading Note 3
date: 2019-01-14 21:12:30
tags:
    - Machine Learning
    - AutoML
    - Meta Heuristic
    - Reinforcement Learning
    - Reading Note
---

This post is about  *Neural Architecture Search* in AutoML, corresponding to Chapter 3 of the book [AutoML](https://www.automl.org/book/). Note that all references can be found in the book.

# What is neural architecture search?

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML3/Intro%20To%20AutoML.001.jpeg)

<!-- more -->

# How to do neural architecture search?

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML3/Intro%20To%20AutoML.002.jpeg)

Search space defines which architectures can be represented in principle. Incorporating prior knowledge about properties well-suited for a task can reduce the size of the search space and simplify the search. However, this also introduces a human bias, which may prevent finding novel architectural building blocks that go beyond the current human knowledge.

The search strategy  details how to explore the search space. It encompasses the classical exploration-exploitation trade-off since, on the one hand, it is desirable to find well-performing architectures quickly, while on the other hand, premature convergence to  a region of suboptimal architectures should be avoided.

The objective of NAS is typically to find architectures that achieve high predictive performance on unseen data. Performance Estimation refers to the process of estimating this performance: the simplest option is to perform a standard training and validation of the architecture on data, but this is unfortunately computationally expensive and limits the number of architectures that can be explored.

## Search space

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML3/Intro%20To%20AutoML.003.jpeg)

The left figure presented chain-structured neural networks that is also the most traditional one. Thus, the search space of NAS must include this type of network. The search space of chain-structured neural networks is then parametrized by:(i) the (maximum) number of layers n (possibly unbounded); (ii) the type of operation every layer can execute , e.g., pooling, convolution, or more advanced layer types like depthwise separable convolutions [13] or dilated convolutions [67]; and (iii) hyperparameters associated with the operation, e.g., number of lters, kernel size and strides for a convolutional layer [4, 58, 10], or simply number of units for fully-connected networks [40].

Another recently emerging network type is cell (also called block) networks, motivated by hand-crafted architectures consisting of repeated motifs [61, 27, 28], Zoph et al. [74] and Zhong et al. [70] propose to search for such motifs, dubbed cells or blocks, respectively, rather than for whole architectures. Zoph et al. [74] optimize two different kind of cells: a normal cell that preservers the dimensionality of the input and a reduction cell which reduces the spatial dimension. The final architecture is then built by stacking these cells in a predefined manner.

## Search strategy

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML3/Intro%20To%20AutoML.004.jpeg)

The search strategy falls into four main categories: randon search, evolutionary methods, reinforcement learning, and gradient based methods. I am most interested with the evolutionary methods and reinforcement learning. Thus I give more details of these two methods in the following.

### Reinforcement learning

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML3/Intro%20To%20AutoML.005.jpeg)

Google brain has published a paper in ICLR 2017, which present how they use reinforcement learning technique to learn a better neural architecture. This paper is based on such an observation that the structure and connectivity of a neural network can be typically specified by a variable-length string.

It is therefore possible to use a recurrent network – the controller – to generate such string. Training the network specified by the string – the “child network” – on the real data will result in an accuracy on a validation set. Using this accuracy as the reward signal, we can compute the policy gradient to update the controller. As a result, in the next iteration, the controller will give higher probabilities to architectures that receive high accuracies. In other words, the controller will learn to improve its search over time.

I summurize this paper as the following points:

> *Flowchart of their algorithm*: In Neural Architecture Search, we use a controller to generate architectural hyperparameters of neural networks. To be flexible, the controller is implemented as a recurrent neural network. Let’s suppose we would like to predict feedforward neural networks with only convolutional layers, we can use the controller to generate their hyperparameters as a sequence of tokens. 
>
> In our experiments, the process of generating an architecture stops if the number of layers exceeds a certain value. This value follows a schedule where we increase it as training progresses. Once the controller RNN finishes generating an architecture, a neural network with this architecture is built and trained. At convergence, the accuracy of the network on a held-out validation set is recorded.
>
> *Parameter update*: The parameters of the controller RNN, $θ_c$, are then optimized in order to maximize the expected validation accuracy of the proposed architectures.
>
> *Parameter update using policy gradient descent* : The list of tokens that the controller predicts can be viewed as a list of actions $a[1:T]$ to design an architecture for a child network. At convergence, this child network will achieve an accuracy $R$ on a held-out dataset. We can use this accuracy $R$ as the reward signal and use reinforcement learning to train the controller. More concretely, to find the optimal architecture, we ask our controller to maximize its expected reward, represented by $J(θ_c)$: Since the reward signal $R$ is non-differentiable, we need to use a policy gradient method to iteratively update $θ_c$.

### Neuro-evolutionary methods

Evolutionary algorithms evolve a population of models, i.e., a set of (possibly trained) networks; in every evolution step, at least one model from the population is sampled and serves as a parent to generate offsprings by applying mutations to it. In the context of NAS, mutations are local operations, such as adding or removing a layer, altering the hyperparameters of a layer, adding skip connections, as well as altering training hyperparameters. After training the offsprings, their fitness (e.g., performance on a validation set) is evaluated and they are added to the population. Here I give two work of neuro-evolutionary methods. The first is from google where they utilize genetic algorithm to find complex classifier comparable to hand-designed models from very simple networks. The latter is from Huawei where they employ genetic algorithm to compress convolutionary networks from very complex network structures.

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML3/Intro%20To%20AutoML.006.jpeg)

*"Large-scale evolution of image classifiers"*: The idea of google's paper was to "sit back" and let evolution at scale do the work of constructing the architecture. Starting from very simple networks, the process found classifiers comparable to hand-designed models at the time.

*"Towards Evolutional Compression"*: Compressing convolutional neural networks (CNNs) is essential for transferring the success of CNNs to a wide variety of applications to mobile devices. In contrast to directly recognizing subtle weights or filters as redundant in a given CNN, this paper presents an evolutionary method to automatically eliminate redundant convolution filters. We represent each compressed network as a binary individual of specific fitness. Then, the population is upgraded at each evolutionary iteration using genetic operations. As a result, an extremely compact CNN is generated using the fittest individual. In this approach, either large or small convolution filters can be redundant, and filters in the compressed network are more distinct. In addition, since the number of filters in each convolutional layer is reduced, the number of filter channels and the size of feature maps are also decreased, naturally improving both the compression and speed-up ratios. Experiments on benchmark deep CNN models suggest the superiority of the proposed algorithm over the state-of-the-art compression methods.

## Performance estimiation strategy

![](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML3/Intro%20To%20AutoML.007.jpeg)

*Lower fidelities:* Such lower delities include shorter training times [74, 68], training on a subset of the data [33], on lower-resolution images [14], or with less filters per layer [74, 48]. While these low-fidelity approximations reduce the computational cost, they also introduce bias in the estimate as performance will typically be underestimated.

*Learning curve extrapolation:* Domhan et al. [19] propose to extrapolate initial learning curves and terminate those predicted to perform poorly to speed up the architecture search process. Swersky et al. [60], Klein et al. [31], Baker et al. [5], Rawal and Miikkulainen [47] also consider architectural hyperparameters for predicting which partial learning curves are most promising.

*Network morphisms:* Another approach to speed up performance estimation is to initialize the weights of novel architectures based on weights of other architectures that have been trained before. One way of achieving this, dubbed network morphisms [63], allows modifying an architecture while leaving the function represented by the network unchanged.

*One-shot architecture search:* One-Shot Architecture Search is another promising approach for speeding up performance estimation, which treats all architectures as different subgraphs of a supergraph (the one-shot model) and shares weights between architectures that have edges of this supergraph in common [51, 9, 45, 38, 6 ]. Only the weights of a single one-shot model need to be trained (in one of various ways), and architectures (which are just subgraphs of the one-shot model) can then be evaluated without any separate training by inheriting trained weights from the one-shot model.