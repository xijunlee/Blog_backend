title: Reunderstanding Evolutionary Algorithm Based on Gradient Descent Method
date: 2018-06-05 23:53:55
tags:
	- Evolutionary Algorithm
	- Gradient Descent
	- Probability Distribution
---

## Evolutionary Algorithm

To be written...

## Natural Gradient Descent

The goal in standard backpropagation is to keep resampling the gradient of the network's parameters after every update, and update them accordingly until reaching a (hopefully global) minimum.

However, there's another way we can think of optimization. To better understand it, we first need to understand KL divergence.

<!-- more -->

In simple terms, KL divergence is a measure of how close a distribution is to another distribution. Here, it's helpful to think of a neural network as a distribution over output values, given an input.

For example, let's take a simple classification network that, given an input image, outputs probabilities that the image is either an apple, a banana, or an orange. If we had two of these networks with the same parameters, their KL divergence would be 0.

On the other hand, if the networks had different parameters, they would likely output different probabilities, given the same image.

The higher the difference between these probabilities are, the higher the KL divergence is between the two networks.

This brings us to the natural gradient. If we blindly update our network in the direction of its gradients, there are no guarantees the distribution of the new network will be similar to the old one.

To fix this, we first consider all combinations of parameters that result in a new network a constant KL divergence away from the old network. This constant value can be viewed as the step size or learning rate. Out of all these possible combinations, we choose the one that minimizes our loss function.

Basically, we're adding in a constraint to our update, that the new network will behave relatively similar to our old one. Our step size corresponds directly to the actual distribution of the network, not it's parameter's values.

This comes with the added benefit of a more stable learning process. Especially when we're updating using a randomly sampled batch, some outliers may make drastic changes to a network's parameters. With natural gradient descent, a single update can't make too much of an impact.

Furthermore, the natural gradient updates based on KL divergence, which only considers the output of a network. It doesn't matter how the network is modeled. Replacing a sigmoid activation with a tanh function would change the standard gradient, but not the natural gradient.

Of course, in practice we don't actually loop through every possible combination of parameters within a certain KL divergence. I won't go into the mathematical explanations here, but it's still true that the natural gradient is more computationally expensive compared to straight gradient descent.

For more in-depth details, check out [this paper](https://arxiv.org/pdf/1301.3584v7.pdf) or [these notes](https://ipvs.informatik.uni-stuttgart.de/mlr/marc/notes/gradientDescent.pdf).

----
Reference
[A intuitive explanation of natural gradient descent](http://kvfrans.com/a-intuitive-explanation-of-natural-gradient-descent/)
