---
title: Reunderstanding The Mathematics Behind Principal Component Analysis
date: 2019-03-10 14:54:13
tags:
    - Machine Learning
    - Optimization
---

As we all know, Principal Component Analysis (PCA) is a dimensionality reduction algorithm that can be used to significantly speed up your unsupervised feature learning algorithm. Most of us just know the procedure of PCA. In other words, we know how to use the algorithm but do not know how it comes. In this post, I summarize the procedure and mathematics of PCA based on materials of reference.

## Procedure of PCA

Suppose we have a dataset  $\{x^{(1)}, x^{(2)},..., x^{(m)} \}$ with n dimension inputs. We want to reduce the data from $n$ dimension to $k$ dimension $(k<<n)$ using PCA. The procedure of PCA is shown below (Owing to the mathematical formual rendering problem, I use picture to display the procedure): 

<!--more-->
{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/20190316Reunderstanding_PCA/Procedure%20of%20PCA.png %}


## Mathematics behind PCA

In the previous section, I present the procedure of PCA.  What a simple algorithm it looks like. However, do we actually know why we calculate the eigenvector of covariance matrix, getting the basis of new subspace.



The motivation of PCA is remaining as much information of raw data as possible by projecting  raw data into lower subspace. In other words, we hope remain as much variance of raw data as possible in the new space.



In the formula (5), we see the projection of raw data. The length (variance) of the projection of $x$ onto $u$ is given by $x^T u$. I.e., if $x^(i)$ is a point in the dataset, then its projection onto $u$ is distance $x^T u$ from the origin. Hence to maximize the variance, we can formulate it as an optimization problem:
$$
\max \quad \frac{1}{m} \Sigma_{i=1}^{m} ({x^{(i)}}^Tu)^2 \tag{6}
$$

$$
s.t. \quad \quad \Arrowvert u \Arrowvert = 1 \tag{7}
$$

This is a maximization problem with constraint. It can be reformulated as:
$$
\max \quad u^T(\frac{1}{m}\Sigma_{i=1}^m x^{(i)} {x^{(i)}}^T )u = u^T\Sigma u \tag{8}
$$

$$
u^Tu=1 \tag{9}
$$

To get the (local) optimum of the constrained problem, we use the method of Lagrange multipliers:
$$
L(u,\lambda) = u^T \Sigma u -\lambda (u^Tu-1) \tag{10}
$$
Note that $\lambda$ is the Lagrange multipliers not the eigenvalue of covariance matrix. Now let us take the partial derivatives of formula (10) with respect to $u$ and $\lambda$. Then let the derivatives equal to zero:
$$
\frac{\partial L}{\partial u} = u^T\Sigma - \lambda u = 0 \tag{11}
$$

$$
\frac{\partial L}{\partial \lambda} = u^Tu-1 = 0 \tag{12}
$$

We can see that the formula (11) is equivalent to formula (3). Thus choosing the new basis of lower space is equivalent to getting the top-k eigenvector of covariance matrix of raw data.



See! The dimensionality reduction problem is intrinsically a constrained maximization problem. We use the method of Lagrangian multiplier to solve the problem.

## Reference

http://59.80.44.100/cs229.stanford.edu/notes/cs229-notes10.pdf

http://ufldl.stanford.edu/wiki/index.php/PCA