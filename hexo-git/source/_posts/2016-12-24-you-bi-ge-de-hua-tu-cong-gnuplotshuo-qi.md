---
layout: post
title: "有逼格的画图从gnuplot说起"
date: 2016-12-24 20:29:04 +0800
comments: true
tags: 
    academic plotting
---

有写论文需求的伙伴们，画图的时候肯定会被导师吐槽过“你这图画的真难看……”。如果不是在写学术论文，`Excel`就能解决大部分需求。但是，如果想画出印刷出版物品质的插图，还是得用到额外的工具。最常见的无非是用`Matlab`来画论文插图。但是，我还是有点嫌弃`Matlab`做出图片的配色和线条粗细（当然，这个可以调整，但每个人审美确实不同）。如果想画出大部分人都觉得精致的插图，该用什么好呢？其实，我之前一段时间是用的`Python`的一个库`matlibplot`,挺方便也挺好看的。但是后来，拜读隔壁大牛实验室[IPADS](http://ipads.se.sjtu.edu.cn)的paper时，总觉得他们的插图很有逼格。这么一打听，才知道是用`gnuplot`画的。于是，我最近的一篇论文就开始全部用`gnuplot`作图了。以后应该是全面倒向`gnuplot`的怀抱。

<!--more-->

此篇文章就简单记录下我使用`gnuplot`的过程，以后边学边update。首先，什么是`gnuplot`？

> Gnuplot is a portable command-line driven graphing utility for Linux, OS/2, MS Windows, OSX, VMS, and many other platforms. The source code is copyrighted but freely distributed (i.e., you don't have to pay for it). It was originally created to allow scientists and students to visualize mathematical functions and data interactively, but has grown to support many non-interactive uses such as web scripting. It is also used as a plotting engine by third-party applications like Octave. Gnuplot has been supported and under active development since 1986.

简单了说`gnuplot`就是一款跨多平台的，支持交互式的，开源的脚本画图语言。如果之前有过`Python`或`Matlab`经验的话，`gnuplot`上手就更快了。下面，给出一些`gnuplot`画图的demo。

{% img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/barchart_art.1.png%}

{% img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/gaussians.png %}


## 安装

`gnuplot`支持多平台，因此安装也是多种多样的，具体参见其[下载页面](http://www.gnuplot.info/download.html)，应当是比较简单的，尤其是在`Windows`下，其安装就是一路点到底。

## 一个demo for `Windows`

因为我论文是在实验室的台式机(`Windows`)上写的。所以，下面的例子就以`Windows`下的`gnuplot`来讲解。

在我论文中，有个需求就是，将折线图和柱状图画在一个坐标系中，二者共用一个横坐标，而左边的纵坐标为柱状图的度量单位，右边的纵坐标为折线图的度量单位，如下图所示:

{% img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/letter.png %}

那么，该如何画这样的图呢？

### 1. 准备好你的数据集

`gnuplot`支持从`.txt`,`.csv`等诸多文件中读取数据。我推荐的方式是用额外的文件保存数据，画图的脚本从该文件中读取数据来进行画图。在我的实践中，我是用`.txt`来保存数据的，如下所示：

```python
letter.txt

1    2.978431    35.85    35.95    35.3
2    5.886818    35.95    36.55    34.9
3    7.494    38.9    39.85    36.45
4    8.354836    39.5    41.65    36.2
5    9.533521    41.9    44.95    36.65
6    10.488704    46    48.1    38.95
7    10.809368    52.9    56.55    42.7
8    10.902275    59.75    66.9    47
9    10.927407    67.95    75.05    50.95
10    10.933407    72.05    80.1    51.45
11    10.936407    75.25    82.8    54.7
12    10.940407    78.65    84.7    56.65
13    10.940407    82    89.45    59.35
14    10.942407    83.5    89.45    64.7
15    10.942407    88    93.5    69.7
16    10.942407    86.65    95.35    69.75
```

其中，第一列是横坐标数，第二列是Entropy，第三到第五列是三个分类器的分类正确率。

### 2. 一个脚本demo

我觉得学习写代码最快的方式就是直接读别人的代码，懂了后，然后就根据自己的需求改。那么下面，我就直接上绘图脚本的代码。

```python
set size ratio 0.8
%设置输出图像的长宽比为0.8

aqua = "#00FFFF"; azure = "#F0FFFF";
aliceblue = "#F0F8FF"
%这是我预设的三种颜色，根据rgb色彩原理

set style fill solid 1.00 border 0

set boxwidth 0.4 relative
%设置柱状图中每条柱子的相对宽度

set xlabel "Attribute number" font "Verdana,15"
%设置x轴标签为"Attribute number" 字体和大小分别为"Verdana" 15

set autoscale

set yrange [2:12];
%设置左纵坐标轴取值范围为[2,12]

set ylabel "Entropy (/bit)" font "Verdana,15";
%设置左纵坐标轴标签为"Entropy (/bit)"

set ytics 2 nomirror;
%设置左纵坐标轴的刻度为2，并且不镜像复制到右边的纵坐标轴，默认情况下是左右纵坐标轴是一样的，但是我们这里的需求是两个纵坐标轴不一样

set y2range [30:100];
%设置右纵坐标轴的取值范围为[30,100]

set y2label "Accuracy (%)" font "Verdana,15"
%设置右纵坐标轴的标签为"Accuracy (%)"

set y2tics 10 nomirror;
%设置右纵坐标轴的刻度为10

set key font "Verdana,15"
%设置图例的字体和大小

set key out horiz
%设置图例在图像的外边，并且呈水平分布

set key center top
%设置图例拜访的位置

set grid ytics
%显示网格

plot "letter.txt" using 2:xtic(1) title "Entropy" with boxes fs pattern 3 lc rgb aliceblue,\           
                          '' using 3 title "SVM" w lp  lw 1.7 pt 2 ps 1.3  axes x1y2, \
                          '' using 4 title "DT" w lp  lw 1.7 pt 8 ps 1.3  axes x1y2, \
                          '' using 5 title "LDA" w lp lw 1.7 pt 12 ps 1.3  axes x1y2

%第48行代码的意思是：从letter.txt读取数据，使用第一列数据作为横坐标，坐标轴名为Entropy,并且柱状图，后面的参数是设置柱状图形状颜色等
%而第49到第51行代码是来画折线图。这里我们以第49行的代码为例子来讲解：使用letter.txt中第三列数据绘制SVM正确率折线图，后面的参数是来设置折线的粗细和点的大小。注意到axes x1y2是指的是该折线图对应的坐标系是共用的横坐标轴和右纵坐标轴。

```

以上便是实现我画图需求的脚本。上述代码中，`%`之后的代码是注释，参照注释应该能明白每一步都是在做什么了吧。具体的参数细节，以后有空补上，或者读者们可以自行Google弄懂之~ 写完脚本后，保存为`letter.plt`文件即可。

### 3. 运行.plt脚本

保存好后`.plt`脚本后，可以打开`gnuplot`交互命令窗口。点击`change dir`，切换到保存你当前脚本的那个文件夹，当然你也可以不切换，但在运行脚本时，就得写上脚本的`绝对路径`。切换好后，运行下面的代码:

```python
load 'letter.plt'
```

然后，你就能生成到一副看上去颇有逼格的图了。最后，你可以选择导出图片，`gnuplot`支持多种格式的图片输出，比如我就选择导出成`.pdf`。

----

好了，以上就是对`gnuplot`的一个简单粗暴的介绍，我也只是刚入门呐。在我个人看来，上述我提及的画图工具，他们制图精美程度的排序是`gnuplot`>`matlibplot`=`Matlab`>`Excel`。当然了，有的大神用`Excel`也能画出很高大上的图，但是前提是要花很多心思和时间在配色上。而`gnuplot`默认的配色和图像设置就能输出令大多数人赏心悦目的图片了。

当然了，最后还得补一句：插图的逼格是次要的，关键还得你paper确实有水平啊~

