---
title: ［iOS开发］推荐一个库，还是解决键盘隐藏问题，好用到飞上天！！
date: 2016-01-2 18:51:33
tags:
	iOS Development
---

最近的这个项目，也是自己第一个iOS开发的项目，开发过程中最头疼的事情就是键盘弹出会遮挡到其他控件的问题。iOS不像android的键盘专门有一个隐藏键盘的按钮，因此给iOS开发者带来很多困扰。昨天刚刚转载了一个方法，但是今天遇到的一个问题就是：当键盘已弹出的情况下，点击另外一个uitextfield弹出一个新的view时（我设置了点击这个uitextfield是不会弹出键盘的，因此这个键盘是已经弹出的键盘），键盘遮挡住了view，试了很多方法都隐藏不了键盘。遇到问题，去git上搜搜总有解决办法，看到github上的一位开发者为iOS开发的模仿android键盘上隐藏键盘的按钮，这下是真的彻底解决了键盘隐藏问题吧！！！

<!-- more -->

这个库就是`hackiftekhar/IQKeyboardManager`，效果图如下：

{%img http://xijun-album.oss-cn-hangzhou.aliyuncs.com/ios_development/img1.jpeg %}


很实用的特性就是红框中标出的两个，一个是在有多个输入框下，可以通过左右键切换输入框，另一个是输入完成，点击done按钮，键盘就会隐藏（相信各位为键盘隐藏头疼的开发者从此不会再头疼了吧）。完整的特性可以去参看原作者的readme，我就稍微多说一下，如何使用这个库(这里针对objective-c，原作者对swift的使用方法说的相当详细了)，相当简单：

- 在你的`Podfile`文件中，添加`pod ‘IQKeyboardManager’`；
- 在shell中，执行`pod install –verbose–no-repo-update`命令，即导入该库到你的project中；
- 来到你的project的`AppDelegate.m`文件中，`import IQKeyboardManager.h`。然后在- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {中键入以下这一行代码：[IQKeyboardManager sharedManager].enable = YES;

至此，就已经成功地将这个库应用到你的project中了，run一下，键盘真的服服帖帖地隐藏了耶，真是好用到飞起来！！！
