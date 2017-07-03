---
title: 卷积神经网络实践之MNIST手写数字体识别
date: 2017-06-23 15:46:26
tags: CNN
---

## 写在前面

之前一直都是只看理论，没有动手，现在终于把理论付诸实践了。话不多说，这篇博文记录一下我在kaggle上实践过程。

## 任务描述

MNIST ("Modified National Institute of Standards and Technology") is the de facto “hello world” dataset of computer vision. Since its release in 1999, this classic dataset of handwritten images has served as the basis for benchmarking classification algorithms. As new machine learning techniques emerge, MNIST remains a reliable resource for researchers and learners alike.

<!-- more -->

In this competition, your goal is to correctly identify digits from a dataset of tens of thousands of handwritten images. We’ve curated a set of tutorial-style kernels which cover everything from regression to neural networks. We encourage you to experiment with different algorithms to learn first-hand what works well and how techniques compare.

## 解决思路

其实这就是个多分类问题，所以常见的分类器都能解决上述问题。kaggle上的大神们也给出了卷积神经网络以外的思路：比如PCA+SVM。类似的还有很多，比如可以PCA+RandomForest, PCA+Xgboost, PCA+blahblahblah ... 

但最牛逼的解决方案还是CNN，我自己写了一个PCA+SVM的kernel，得分比CNN低很多。当然，如果做好feature engeering和好好调参的话，应该还是能取得不错的效果。但是肯定不如CNN厉害，不然现在CNN为什么会在图像识别领域大火呢？

## 实践

要我手写一个CNN是不可能的，既太花时间，我也暂时没那个本事。所以，我就用了Keras+ TensorFlow来实现一个卷积神经网络。

官网介绍：[Keras](https://keras.io) is a high-level neural networks API, written in Python and capable of running on top of either TensorFlow, CNTK or Theano. It was developed with a focus on enabling fast experimentation. Being able to go from idea to result with the least possible delay is key to doing good research.

官网介绍：[TensorFlow](https://www.tensorflow.org) is an open source software library for numerical computation using data flow graphs. Nodes in the graph represent mathematical operations, while the graph edges represent the multidimensional data arrays (tensors) that flow between them. This flexible architecture lets you deploy computation to one or more CPUs or GPUs in a desktop, server, or mobile device without rewriting code. TensorFlow also includes TensorBoard, a data visualization toolkit.

Keras是一个高层次抽象的卷积神经网络python库，但它本身是不会做矩阵计算等任务的，需要以TensorFlow作为后端来进行复杂的底层计算。在TensorFlow的基础上，Keras能非常方便地调用各种api来完成卷积神经网络的搭建。下面，简单地记录一下安装Kera+TensorFlow的过程。

### TensorFlow安装 (on Ubuntu without GPU support)
这里，我用的系统是Ubuntu 14.04.1 LTS (GNU/Linux 3.13.0-32-generic x86_64), python是3.4.3，GCC是4.8.4，所以下面的安装就是基于以上环境。建议采用pip的方式安装TensorFlow，下面keras也一样，简直是太方便了。因为实验室服务器没有GPU，所以我就只能安装CPU版本的TensorFlow了。

1.创建虚拟环境

为不同的项目创建独立的python虚拟环境是一个良好的习惯。利用不同的python虚拟环境来做不同的事，以免不同项目的环境相互影响了。以下两行代码分别是安装python 2.7和python 3.4+虚拟环境的命令，任君选择。
```bash
$ sudo apt-get install python-pip python-dev python-virtualenv    # python 2.7
$ sudo apt-get install python3-pip python3-dev python3-virtualenv # python 3.4+
```

2.启动虚拟环境
```bash
$ virtualenv --system-site-packages ~/tensorflow
$ source ~/tensorflow/bin/activate
```

3.安装TensorFlow
```bash
# Python 2
$ sudo pip install --upgrade tensorflow
 
# Python 3
$ sudo pip3 install --upgrade tensorflow
```

### Keras安装

1.安装所需要的包和keras

```bash
$ pip install numpy scipy
$ pip install scikit-learn
$ pip install pillow
$ pip install h5py
$ pip install keras
```

2.编辑配置文件keras.json，将keras的计算后端设置为TensorFlow：
```bash
vim ~/.keras/keras.json
```
确保上述文件内容为:
```json
{
    "image_dim_ordering": "tf", 
    "epsilon": 1e-07, 
    "floatx": "float32", 
    "backend": "tensorflow"
}
```
image_dim_ordering：TensorFlow是使用NumPy数组 (height, width, depth)。如果你用Theano，数组表示方法(depth, height, width)。

打开python，输入以下代码
```python
import Keras
```
如果出现以下代码，表示基于TensorFlow的Keras安装成功！！
```python
>>> import keras
Using TensorFlow backend.
```

## 实现与结果
这里给出我利用keras实现的一个多层卷积神经网络，源码戳[这里](https://github.com/xijunlee/kaggle-solution/blob/master/DigitRecognizer/DigitRec_CNN.py)，该网络的结构如下：
```txt
_________________________________________________________________
Layer (type)                 Output Shape              Param #
=================================================================
conv2d_1 (Conv2D)            (None, 28, 28, 32)        320
_________________________________________________________________
conv2d_2 (Conv2D)            (None, 28, 28, 32)        9248
_________________________________________________________________
max_pooling2d_1 (MaxPooling2 (None, 14, 14, 32)        0
_________________________________________________________________
conv2d_3 (Conv2D)            (None, 14, 14, 64)        18496
_________________________________________________________________
conv2d_4 (Conv2D)            (None, 14, 14, 64)        36928
_________________________________________________________________
max_pooling2d_2 (MaxPooling2 (None, 7, 7, 64)          0
_________________________________________________________________
conv2d_5 (Conv2D)            (None, 7, 7, 128)         73856
_________________________________________________________________
conv2d_6 (Conv2D)            (None, 7, 7, 128)         147584
_________________________________________________________________
conv2d_7 (Conv2D)            (None, 7, 7, 128)         147584
_________________________________________________________________
conv2d_8 (Conv2D)            (None, 7, 7, 128)         147584
_________________________________________________________________
max_pooling2d_3 (MaxPooling2 (None, 3, 3, 128)         0
_________________________________________________________________
conv2d_9 (Conv2D)            (None, 3, 3, 256)         295168
_________________________________________________________________
conv2d_10 (Conv2D)           (None, 3, 3, 256)         590080
_________________________________________________________________
conv2d_11 (Conv2D)           (None, 3, 3, 256)         590080
_________________________________________________________________
conv2d_12 (Conv2D)           (None, 3, 3, 256)         590080
_________________________________________________________________
max_pooling2d_4 (MaxPooling2 (None, 1, 1, 256)         0
_________________________________________________________________
flatten_1 (Flatten)          (None, 256)               0
_________________________________________________________________
dropout_1 (Dropout)          (None, 256)               0
_________________________________________________________________
dense_1 (Dense)              (None, 128)               32896
_________________________________________________________________
dense_2 (Dense)              (None, 10)                1290
=================================================================
Total params: 2,681,194
Trainable params: 2,681,194
Non-trainable params: 0
```
我是故意把这个搞的比较复杂，最后kaggle上LB得分为0.98971, rank 652/1924，很多人做到了score为1的地步……

我前两个卷积层为例说明一下卷积神经网络中参数个数的理解。

首先，我的输入是一个28x28的灰度图，那么输入层是一个28x28x1的结构，那个1是该层的深度。

conv2d_1：第一个卷积层，总共设置了32个filters(滤波器，也叫卷积核，这家伙也是有多个马甲的)。卷积核大小设置为3x3。那么经过32个filter的卷积后，输入层的图像变成了32个28x28的卷积层。（注意filter的数量等于该层的深度）。那么输入层到第一层卷积层的参数数量是怎么计算的呢？因为每个卷积核有3x3个参数，然后每个卷积核还要带一个bias参数，所以一个卷积核有3x3x1+1=10个参数。conv2d_1总共有32个卷积核，并且每个卷积核的深度是1，所以总共有(3x3x1+1)x32=320个参数。这个1看起来乘地没必要，但是它代表了每个卷积核的深度。卷积核的深度与上一层的输出的深度相等。

con2d_2：第二个卷积层，同样设置了32个filters，卷积核大小同样设置为3x3。conv2d_1的输出是28x28x32的结构，即深度为32的28x28的图像。自然，con2d_2的filter的深度也为32。经过con2d_2的32个filter后，卷积得到依旧是28x28x32的输出。参数的数量怎么计算呢？对于con2d_2的一个卷积核来说，其深度是32，每一层的大小是3x3，再外加一个bias参数，那么一个卷积核的参数数量便是3x3x32+1=289个。然后总共有32个卷积核，所以总共的参数数量是(3x3x32+1)x32=9248。

可能con2d_2的参数数量说明的让人头晕，更详细和生动的说明可以参见[博文](https://www.zybuluo.com/hanbingtao/note/485480)，里面有动图说明了对于深度大于1的卷积核的卷积操作。


### 关于引入冲量

本小节内容转载自 @辛淼 CaffeCN社区（caffecn.cn）

随机梯度下降（SGD）是按batch来进行更新，通常来说下降速度比较快，但却容易造成另一个问题，就是更新过程不稳定，容易出现震荡。

引入momentum的idea是很直接的，就是在更新下降方向的时候不仅要考虑到当前的方向，也要考虑到上一次的更新方向，两者加权，某些情况下可以避免震荡。冲量，就是**上一次更新方向所占的权值**。

一个小的trick是，当刚开始训练的时候，把冲量设小，或者直接就置为0，然后慢慢增大冲量，有时候效果比较好。

### PCA+SVM的实现

之后，我又尝试了PCA+SVM的模型，源码戳[这里](https://github.com/xijunlee/kaggle-solution/blob/master/DigitRecognizer/DigitRec_SVM.py)。因为没有专门去调参数，所以LB得分很低，才0.49743…… 不过，我想就算找到了最优参数，其效果也应该比不上卷积神经网络。

## DNN零基础入门教程推荐

最后，推荐一组深度学习的入门教程，我认为还不错。

[零基础入门深度学习(1) - 感知器](https://www.zybuluo.com/hanbingtao/note/433855)
[零基础入门深度学习(2) - 线性单元和梯度下降](https://www.zybuluo.com/hanbingtao/note/448086)
[零基础入门深度学习(3) - 神经网络和反向传播算法](https://www.zybuluo.com/hanbingtao/note/476663)
[零基础入门深度学习(4) - 卷积神经网络](https://www.zybuluo.com/hanbingtao/note/485480)
[零基础入门深度学习(5) - 循环神经网络](https://zybuluo.com/hanbingtao/note/541458)
[零基础入门深度学习(6) - 长短时记忆网络(LSTM)](https://zybuluo.com/hanbingtao/note/581764)
[零基础入门深度学习(7) - 递归神经网络](https://zybuluo.com/hanbingtao/note/626300)




---
Reference
https://www.kaggle.com/poonaml/deep-neural-network-keras-way
http://blog.topspeedsnail.com/archives/10427
https://keras.io
https://www.kaggle.com/somshubramajumdar/deep-convolutional-network-using-keras
https://www.zybuluo.com/hanbingtao/note/485480
