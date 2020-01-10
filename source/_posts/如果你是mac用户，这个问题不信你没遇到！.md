---
title: 如果你是mac用户，这个问题不信你没遇到！
date: 2020-01-07 19:45:01
tags:
- MacOS
- python
- virtualenv
categories:
- Mac

---

Mac 用户在使用 python 的虚拟环境时，你现在或者将来一定会遇到此问题，早发现早治疗 ～

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

### 1.问题

在虚拟环境中执行 `python` 等相关命令时，报如下所示错误：

<img src="https://github.com/EthanYan6/pic/raw/master/python%E8%99%9A%E6%8B%9F%E7%8E%AF%E5%A2%83%E6%8A%A5%E9%94%99-2.png" alt="报错2" style="zoom:50%;" />

使用 `pip` 命令报错依旧：

<img src="https://github.com/EthanYan6/pic/raw/master/python%E8%99%9A%E6%8B%9F%E7%8E%AF%E5%A2%83%E6%8A%A5%E9%94%99-1.png" alt="报错1" style="zoom:50%;" />

是不是很诡异？

### 2.原因分析

出现上述问题，在查询过 `Google` 和 `stackoverflow` 等之后，发现了共性：都是使用 `MAC` 的用户，而且使用了  `brew` 包管理工具。

在执行 `brew update` 命令之后会升级所有管理的模块，比如 `Python` 。因此遇到 `Python` 有版本更新时，嘻嘻，恭喜你中奖了。

`Mac` 在创建虚拟环境后，会发现文件夹下有一些链接文件，各种软链接其实指向了系统的 `Python` （节省空间所致），所以当你更新了 `Python` 之后，它所在的路径中包含的版本号也会更新，路径立即失效（软链接其实就保存的就是绝对路径），你的虚拟环境也就无法正常启动了。

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

### 3.解决办法

网上有一些解决办法，如下：

1.既然知道原因，那么重新修复这些软链接不就好了？于是，开始了 `删除创建` 之旅。

> 我滴个乖乖！你知道哪个犄角旮旯里还藏着一个不怀好意的软链接嘛？然后网友提供了各种命令：
>
> `find ~/.virtualenvs/my-virtual-env/ -type l` 别闹了好吗？几十个文件，我怎么弄？放弃放弃，如果你愿意，那么请查看这篇帖子仔细研究即可：https://stackoverflow.com/questions/23233252/broken-references-in-virtualenvs

2.重新装系统

> 这个。。。不至于吧，杀鸡焉用宰牛刀？既然知道问题，解决就好了嘛

3.删除虚拟环境

> 这个听起来不错，这也正是我想讲的方法。前提是你有 `requirements.txt` 文件，要不然还是麻烦点使用办法 1 吧。

### 4.实操

删除重建虚拟环境即可，但如果这么简单，还用往下讲解吗？笑话！以后再出现，难不成还在删除吗？且听我的骚操作。

删除虚拟环境后，再创建时，请使用如下命令：

```
virtualenv --no-site-packages --always-copy 虚拟环境名称 -p python3
```

1.`--no-site-packages` ：创建虚拟环境时，不会安装系统环境中的任何模块（也就是创建一个全新的环境）

2.`-p python3` 指定创建的虚拟环境为 `python3.x` 版本

3.`--always-copy` 可以跟踪系统 `python` 信息，即使你升级了系统的 `python` ，虚拟环境仍然可以使用。

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***