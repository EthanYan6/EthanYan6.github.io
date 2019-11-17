---
title: MacBook Pro你真的会使用吗？
date: 2019-11-17 14:58:48
tags:
- MacOS
- 快捷键
- MacBook Pro
categories:
- Mac
---

 <center>Author：闫玉良</center> 
那些不使用鼠标的人是否是打肿脸充胖子？因囊中羞涩而安装的xx软件装不上怎么办？MacBook Pro真的那么好用吗？下面为您解答

<!--more-->

经过不懈的努力，小闫同学终于使用上了 MacBook Pro，先小小的炫耀一番。（不要打我哈~）

![小闫同学的 MacBook Pro](https://github.com/EthanYan6/pic/raw/master/MacBookPro%E4%BD%A0%E7%9C%9F%E7%9A%84%E4%BC%9A%E4%BD%BF%E7%94%A8%E5%90%97%EF%BC%9F/IMG_0212.jpg)

![小闫同学的 MacBook Pro](https://github.com/EthanYan6/pic/raw/master/MacBookPro%E4%BD%A0%E7%9C%9F%E7%9A%84%E4%BC%9A%E4%BD%BF%E7%94%A8%E5%90%97%EF%BC%9F/IMG_0211.jpg)

激动的心，颤抖的手，打开了笔记本才发现我是个弟弟，怎么用？经过了一番研究，将电脑弄死好几次的探索，终于入了门。分享给大家吧。

首先 macOS 是类 Linux 系统，因此会命令的童鞋超级好上手。其次，都说Mac 可以提高生产力，可以解放鼠标，怎么做到的呢？那就是超级丰富的快捷键以及非常大块且灵敏的触摸板的功劳了。

一拿到手，第一件事请将触摸板设置成 **tap to click（单击）**。设置方式为依次点击如下按钮：左上角的苹果logo -> System Preferences -> trackpad -> 勾选 tap to click。（这样就不需要点按触摸板了）

第二件事请打开 **three finger drag （三指拖拽）**功能。设置方式如下：左上角的苹果logo -> System Preferences -> Accessibility -> Pointer control -> 点击右方左下角的 trackpad options -> 勾选 enable dragging -> 选择 three finger drag。

第三件事请设置 **Hot corners （触摸角功能）**。设置方式如下：在桌面任意位置鼠标右键（或者触摸板双指触碰一下）-> change desktop background -> screen saver -> Hot corners。我将右下角设置为了 Lock Screen ，只要鼠标往右下角一划或者在触摸板上往右下角一划即可锁屏走人。

这都是一些简单但是非常提高效率设置，那么 Mac 上是如何提高生产力的呢？光这些可不够，下面再介绍一些常用操作。

在 Mac 上面，command 键等同于 Windows 上的 control 键，所以复制粘贴你懂得 ~ 下面快来学习一些 Mac 的小知识吧！

## 快捷键

1.快速将当前窗口最大化： 

`control + command + F `

再次按上述快捷键即可恢复原样 

2.快速将当前窗口最小化： 

`command + M `

> 如果当前窗口处于最大化状态，那么此命令无效 

3.快速将最小化的窗口调出来： 

​	3.1 先使用如下快捷键，打开所有的应用

```shell
command + tab 
```

​	3.2 按住 command 键不松开，按 tab 键向后选择，按 shift + tab 键向前选择

​	3.3 选中图标之后按住 option 键，松开 command 键即可打开应用

4.类似 home 键和 end 键的快捷操作：

​	4.1 快速移动到行首：

```shell
control + A
```

​	4.2 快速移动到行尾：

```shell
control + E
```

​	4.3 到下一行：

```shell
control + N
```

​	4.4 到上一行：

```shel
control + P
```

​	4.5 从光标处开始删除，知道行尾：

```shell
control + K
```



## 安装软件出现文件已损坏

1.关闭软件，点击取消，先别着急移到废纸篓 

2.输入如下命令，开启安装任何应用：

```shell
sudo spctl --master-disable 
```

3.如果是10.15的新系统，有可能还是失败，那么输入如下命令即可解决： 

```shell
xattr -cr /Applications/MindNode.app
```

​	3.1 上方的命令是先输入：

```shell
	xattr -cr
```

​	3.2 然后将损坏的应用图标拖到终端命令后，按回车即可 