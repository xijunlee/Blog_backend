---
layout: post
title: "玩Hadoop中遇到的坑"
date: 2016-12-21 10:46:30 +0800
comments: true
tags:
    distributed computing
---

专门开一篇记录在玩Hadoop中遇到的坑，既给自己做个笔记，也为后来者做个提醒。

按照[官方指南](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html)所给的步骤，一直执行到step 5之前都没什么问题，但是当执行到
```c
$ bin/hdfs dfs -put etc/hadoop input
```
<!--more-->

就报出如下错误:
```c
put: 'input': No such file or directory
```

我的解决方案：
在step 4中,
```c
$ bin/hdfs dfs -mkdir /user $ bin/hdfs dfs -mkdir /user/<username> 
``` 这里就要注意了，```<username>```这里最好填你当前登录```ubuntu```的用户名(比如```root```)，因为后面使用 
```c
$ bin/hdfs dfs -put etc/hadoop input 
```
不指明绝对路径的话，```Hadoop```的相对路径默认的是从```/home/[username]```开始的，也就是```/home/root/etc/hadoop```, 如果创建文件夹时不是```root```的话，那自然找不到文件咯。所以，要么填当前操作```ubuntu```的用户名，要么乖乖地写全路径。
