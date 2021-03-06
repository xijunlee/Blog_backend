title: 卷积神经网络为什么叫卷积呢？
date: 2017-04-11 22:45:16
tags: 
	- CNN
	- Machine Learning
---

## 写在前面

之前读过好一些关于卷积神经网络的入门文章，看完了之后还是觉得似懂非懂。特别是**卷积网络为什么叫卷积**呢？上次吃饭居然还跟小红他们就这个问题聊了一下。今天碰巧刷知乎刷到[这篇文章](https://zhuanlan.zhihu.com/p/25249694)，作者对CNN做了非常生动的解释，是我目前看到的关于CNN最好的中文解释。当然了，作者还就卷积网络中卷积层对图像特征的提取和池化层的降维做了很好的解释。文章最后又对一个已经学习好的CNN中的卷积层和全连接层进行了可视化，这让我这样的小白能非常直观地看出CNN到底最后学出了什么特征。这篇博文整理了原文中关于解释**卷积神经网络为什么叫卷积**的描述，更多内容可以直接去[原文](https://zhuanlan.zhihu.com/p/25249694)中。

## 卷积

我们在 2 维上说话。有两个$\mathcal{R}^2\rightarrow \mathcal{R}$的函数 f(x, y) 和 g(x, y) 。所谓 f 和 g 的卷积就是一个新的 $\mathcal{R}^2\rightarrow \mathcal{R}$的函数 c(x, y) 。通过下式得到：

<!-- more -->

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/intro_to_cnn/eq1.png %}


这式子的含义是：遍览从负无穷到正无穷的全部 s 和 t 值，把 g 在 (x-s, y-t) 上的值乘以 f 在 (s, t) 上的值之后再“加和”到一起（积分意义上），得到 c 在 (x, y) 上的值。说白了卷积就是一种“加权求和”，f 为权。以 (x, y) 为中心，把 g 距离中心 (-s, -t) 位置上的值乘上 f 在 (s, t) 的值，最后加到一起。把卷积公式写成离散形式就更清楚了：

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/intro_to_cnn/eq2.png %}

第二个等号成立是因为在这里我们每隔单位长度 1 一采样，$\Delta s$和$\Delta t$都是 1 。可以令 G 表示一幅 100 x 100 大小的灰度图像。G(x, y) 取值 [0,255] 区间内的整数，是图像在 (x, y) 的灰度值。x 和 y 坐标取 [0, 99] ，其它位置上 G 值全取 0 。令 F 在 s 和 t 取 {-1, 0, 1} 的位置为特定值，其他位置全取 0 。F 可以看作是一个 3 x 3 的网格。如图 1.1


{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/intro_to_cnn/1.1.png %}
图 1.1


图 1.1 中 G 每个小格子里的值就是图像在 (x, y) 的灰度值。F 每个小格子里的值就是 F 在 (s, t) 的取值。


{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/intro_to_cnn/1.2.jpg %}
图 1.2


如图 1.2 所示，将 F 的中心 (0, 0) 对准 G 的 (6, 6) 。把 F 和 G 对应的 9 个位置上各自的值相乘，再将 9 个乘积加在一起，就得到了卷积值 C(6, 6) 。对 G 的每一个位置求 C 值，就得到了一幅新的图像。其中注意三点：

F 是上下左右翻转后再与 G 对准的。因为卷积公式中 F(s, t) 乘上的是 G(x-s, y-t) 。比如 F(-1, -1) 乘上的是 G(7, 7) ；
如果 F 的所有值之和不等于 1.0，则 C 值有可能不落在 [0, 255] 区间内，那就不是一个合法的图像灰度值。所以如果需要让结果是一幅图像，就得将 F 归一化——令它的所有位置之和等于 1.0 ；
对于 G 边缘上的点，有可能它的周围位置超出了图像边缘。此时可以把图像边缘之外的值当做 0 。或者只计算其周围都不超边缘的点的 C 。这样计算出来的图像就比原图像小一些。在上例中是小了一圈，如果 F 覆盖范围更大，那么小的圈数更多。
上述操作其实就是对数字图像进行离散卷积操作，又叫滤波。F 称作卷积核或滤波器。不同的滤波器起不同的作用。想象一下，如果 F 的大小是 3 x 3 ，每个格子里的值都是 1/9 。那么滤波就相当于对原图像每一个点计算它周围 3 x 3 范围内 9 个图像点的灰度平均值。这应该是一种模糊。看看效果。

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/intro_to_cnn/1.3.png %}
图 1.3


左图是 lena 灰度原图。中图用 3 x 3 值都为 1/9 的滤波器去滤，得到一个轻微模糊的图像。模糊程度不高是因为滤波器覆盖范围小。右图选取了 9 x 9 值为 1/81 的滤波器，模糊效果就较明显了。滤波器还有许多其他用处。例如下面这个滤波器：

+---+---+---+
| -1 |  0 |  1 |
+---+---+---+
| -2 |  0 |  2 |
+---+---+---+
| -1 |  0 |  1 |
+---+---+---+
注意该滤波器没有归一化（和不是 1.0 ），故滤出来的值可能不在 [0, 255] 之内。通过减去最小值、除以最大／最小值之差、再乘以 255 并取整，把结果值归一到 [0, 255] 之内，使之成为一幅灰度图像。现在尝试用它来滤 lena 图。

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/intro_to_cnn/1.4.png %}
图 1.4


该滤波器把图像的边缘检测出来了。它就是 Sobel 算子。边缘检测、图像模糊等等都是人们设计出来的、有专门用途的滤波器。如果搞一个 9 x 9 的随机滤波器，会是什么效果呢？

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/intro_to_cnn/1.5.png %}
图 1.5


如上图，效果也类似于模糊。因为把一个像素点的值用它周围 9 x 9 范围的值随机加权求和，相当于“捣浆糊”。但可以看出模糊得并不润滑。

这时我们不禁要想，如果不是由人来设计一个滤波器，而是从一个随机滤波器开始，根据某种目标、用某种方法去逐渐调整它，直到它接近我们想要的样子，可行么？这就是卷积神经网络（Convolutional Neural Network, CNN）的思想了。可调整的**滤波器**是 CNN 的**“卷积”**那部分；如何**调整滤波器**则是 CNN 的**“神经网络”**那部分。

## 卷积神经网络

现在把卷积滤波器和神经网络两个思想结合起来。卷积滤波器无非就是一套权值。而神经网络也可以有（除全连接外的）其它拓扑结构。可以构造如图 3.1 所示意的神经网络。

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/intro_to_cnn/3.1.jpg %}
图 3.1


该神经网络接受 n x n 个输入，产生 n x n 个输出。图中左边的平面包含 n x n 个格子，每个格子中是一个 [0, 255] 的整数值。它就是输入图像，也是这个神经网络的输入。右边的平面也是 n x n 个格子，每个格子是一个神经元。每个神经元连接到输入上它对应位置周围 3 x 3 范围内的值。每个连接有一个权值。所有神经元都如此连接（图中只画了一个，出了输入图像边缘的连接就认为连接到常数 0 ）。右边层的 n x n 个神经元的输出就是该神经网络的输出。

这个神经网络有两点与全连接神经网络不同。首先它不是全连接的。右层的神经元并非连接上全部输入，而是只连接了一部分。这里的一部分就是输入图像的一个局部区域。我们常听说 CNN 能够把握图像局部特征就是这个意思。这样一来权值少了很多，因为连接就少了。权值其实还更少，因为每一个神经元的 9 个权值都是和其他神经元共享的。全部 n x n 个神经元都用这共同的一组 9 个权值，并且不要偏置值。那么这个神经网络其实一共只有 9 个参数需要调整。

看了第一节的同学们都看出来了，这个神经网络不就是一个卷积滤波器么？只不过卷积核的参数未定，需要我们去训练——它是一个“可训练滤波器”。这个神经网络其实就是一个只有一个卷积层、且该卷积层只有一个滤波器（通道）的 CNN 。

试着用 Sobel 算子滤出来的图片作为目标值去训练这个神经网络。给神经网络的输入是灰度 lena 图，目标输出是经过 Sobel 算子滤波的 lena 图，见图 1.4 。这唯一的一对输入输出图片就构成了训练集。神经网络权值随机初始化，训练 2000 轮。如图 3.7 。

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/intro_to_cnn/3.2.jpg %}
图 3.2


从左上到右下依次为：初始随机滤波器输出、每个 200 轮训练后的滤波器输出（ 10 幅）、最后一幅是 Sobel 算子的输出，也就是用作训练的目标图像。可以看到经过最初 200 轮后，神经网络的输出就已经和 Sobel 算子的输出看不出什么差别了。后面那些轮的输出基本一样。输入与输出的均方误差 mse 随着训练轮次的变化。如图 3.3 。

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/intro_to_cnn/3.3.jpg %}
图 3.3


1500 轮过后，mse 基本就是 0 了。训练完成后网络的权值是：

+-------------------+--------------------+--------------------+
| 1.2886529   |  0.04068733  |  -1.3082279  |
+-------------------+--------------------+--------------------+
| 1.43157125  |  0.01173212  |  -1.45389295 |
+-------------------+--------------------+--------------------+
| 1.34158182  |  -0.07245208 |  -1.27504027 |
+-------------------+--------------------+--------------------+
与 Sobel 算子比较一下：

+---+---+---+
| -1 |  0 |  1 |
+---+---+---+
| -2 |  0 |  2 |
+---+---+---+
| -1 |  0 |  1 |
+---+---+---+

注意训练出来的滤波器负数列在右侧而不是左侧。因为计算卷积是把滤波器上下左右翻转反着扣上去的。这并不重要，本质是相同的。关键是一正列、一负列，中间零值列。非零值列三个值之比近似 1:2:1 。我们得到的就是一个近似的 Sobel 算子。我们以训练神经网络的方式把一个随机滤波器训练成了 Sobel 算子。这就是优化的魔力（代码见本文最后）。

在 CNN 中，这样的滤波器层叫做卷积层。一个卷积层可以有多个滤波器，每一个叫做一个通道，或者叫做一个 feature map 。可以给卷积层的输出施加某个激活函数：Sigmoid 、Tanh 等等。激活函数也构成 CNN 的一层——激活层，这样的层没有可训练的参数。

还有一种层叫做 Pooling 层（池化层）。它也没有参数，起到降维的作用。将输入切分成不重叠的一些 n x n 区域。每一个区域就包含 n x n 个值。从这 n x n 个值计算出一个值。计算方法可以是求平均、取最大等等。假设 n = 2，那么 4 个输入变成一个输出。输出图像就是输入图像的 1/4 大小。若把 2 维的层展平成一维向量，后面可再连接一个全连接前向神经网络。

通过把这些组件进行组合就得到了一个 CNN 。它直接以原始图像为输入，以最终的回归或分类问题的结论为输出，内部兼有滤波图像处理和函数拟合，所有参数放在一起训练。这就是卷积神经网络。

以上提供了一个理解 CNN 的视角。CNN 的发明并非沿着此思路。像 NN 本身一样，CNN 的思想来源于对生物视觉神经系统的研究。

----
Reference
［1］[《最优化导论》（美）Edwin K. P. Chong（美） Stanislaw H. Zak](https://link.zhihu.com/?target=https%3A//book.douban.com/subject/26732914/)
［2］[《神经网络设计》（美）Martin T. Hagan（美）Howard B. Demuth（美）Mark Beale](https://link.zhihu.com/?target=https%3A//book.douban.com/subject/1115600/)
［3］[《机器学习》（美）Tom Mitchell](https://link.zhihu.com/?target=https%3A//book.douban.com/subject/1102235/)
［4］[《神经计算原理》（美）Fredric M. Han（美）Ivica Kostanic](https://link.zhihu.com/?target=https%3A//book.douban.com/subject/2068931/)
［5］[《人工智能——复杂问题求解的结构和策略》（美）George F. Luger](https://link.zhihu.com/?target=https%3A//book.douban.com/subject/3587732/)


