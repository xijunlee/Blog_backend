---
title: AutoML Reading Note 2
date: 2018-12-09 23:41:25
tags:
    - Machine Learning
    - AutoML
    - Optimization
    - Reading Note
    - Meta Learning
---

This post is about meta learning in AutoML, corresponding to Chapter 2 of the book [AutoML](https://www.automl.org/book/). Note that all references can be found in the book.

# What is meta learning?

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.017.jpeg)

<!-- more -->

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.018.jpeg)

# Challenges of meta learning

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.019.jpeg)

# How to achieve meta learning?

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.020.jpeg)

## Learning from model evaluation

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.021.jpeg)

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.022.jpeg)

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.023.jpeg)



1. Relative landmarks: a first measure for task similarity considers the relative (pairwise) performance difference.
2. Surrogate models: a more flexible way to transfer information is to build surrogate model s_j(\theta_i) = P_{i,j} for all prior task, trained using all available P. One can the define task similarity in terms of the error between s_j(\theta_i) and P_{i,new}: if the surrogate model for t_j can generate accurate prediction for t_{new}, then those tasks are intrinsitically similar. This is usually done in combination with Bayesian optimization to determine the next \theta.
3. Warm-started multi-task learning: another approach to relate prior tasks tj is to learn a joint task representation using P. In [114], task-specic Bayesian linear regression [20] surrogate models sj(i) are trained and combined in a feedforward Neural Network NN(i) which learns a joint task representation that can accurately predict Pi;new. The surrogate models are pre-trained on OpenML meta-data to provide a warm-start for optimizing NN(i) in a multi-task learning setting. Earlier work on multitask learning [165] assumed that we already have a set of `similar' source tasks tj . It transfers information between these tj and tnew by building a joint GP model for Bayesian optimization that learns and exploits the exact relationship between the tasks.
4. Multi-armed bandits: another approach to relate prior tasks tj is to learn a joint task representation using P. In [114], task-specic Bayesian linear regression [20] surrogate models sj(i) are trained and combined in a feedforward Neural Network NN(i) which learns a joint task representation that can accurately predict Pi;new. The surrogate models are pre-trained on OpenML meta-data to provide a warm-start for optimizing NN(i) in a multi-task learning setting. Earlier work on multitask learning [165] assumed that we already have a set of `similar' source tasks tj . It transfers information between these tj and tnew by building a joint GP model for Bayesian optimization that learns and exploits the exact relationshipbetween the tasks.



## Learning from task properties

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.024.jpeg)

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.025.jpeg)

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.026.jpeg)

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.027.jpeg)

Self-play reinforcement learning approach: AlphaD3M [38] uses a self-play reinforcement learning approach in which the current state is represented by the current pipeline, and actions include the addition, deletion, or replacement of pipeline components. A Monte Carlo Tree Search (MCTS) generates pipelines, which are evaluated to train a recurrent neural network (LSTM) that can predict pipeline performance, in turn producing the action probabilities for the MCTS in the next round. The state description also includes meta-features of the current task, allowing the neural network to learn across tasks.



## Learning from prior models

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.028.jpeg)

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.029.jpeg)

### Transfer learning

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.030.jpeg)

In transfer learning, we take models trained on one or more source tasks, and use them as starting points for creating a model on a similar target task. This can be done by forcing the target model to be structurally or otherwise similar to the source models.

Neural networks are exceptionally suitable for transfer learning because both the structure and the model parameters of the source models can be used as a good initialization for the target model, yielding a pre-trained model which can be further fine-tuned using available training data on new task.



### Meta learning& few-shot learning

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.031.jpeg)

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.032.jpeg)

This paper is based on the motivation that the parameter update of base learner resembles the updates for cell state in an LSTM. Thus they propose an LSTM-based meta-learner optimizer that is trained to optimize a learner neural network classifier. The meta-learner captures both short-term knowledge within a task and long-term knowledge common among all the tasks. By using an objective that directly captures an optimization algorithm’s ability to have good generalization performance given only a set number of updates, the meta learner model is trained to converge a learner classifier to good solution quickly on each task. Additionally, the formulation of the meta-learner model allows it to learn a task-common initialization for the learner classifier, which captures fundamental knowledge shared among all the tasks.

![](http://xijun-album.oss-cn-hangzhou.aliyuncs.com/AutoML2/Intro%20To%20AutoML.033.jpeg)

Model-Agnostic Meta-Learning (MAML) provides a good initialization of a model’s parameters to achieve an optimal fast learning on a new task with only a small number of gradient steps while avoiding overfitting that may happen when using a small dataset.

In the diagram above, θ is the model’s parameters and the bold black line is the meta-learning phase. When we have, for example, 3 different new tasks 1, 2 and 3, a gradient step is taken for each task (the gray lines). We can see that the parameters θ are close to all the 3 optimal parameters of task 1, 2, and 3 which makes θ the best parameters initialization that can quickly adapt to different new tasks. As a result, only a small change in the parameters θ will lead to an optimal minimization of the loss function of any task.