---
title: Tmux让你开发效率飞起
date: 2020-06-25 11:32:52
tags: tmux
categories: tmux
---

<center>Author：闫玉良</center>

`ssh` 连接突然中断，之前操作记录丢失怎么办？终端不好管理，分窗口太乱怎么办？分屏幕操作切到眼花缭乱怎么办？鼠标键盘配合手忙脚乱怎么办？快来试试开发辅助神器 `Tmux` 吧！

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.简介

你可以将 `Tmux` 称为一个分屏神器，但这有些以偏概全。因为它既可以有效的管理终端，又可以保存会话。它采用了 `C/S` 架构，使会话与终端窗口分离。这么说有些难理解，下面举个例子：

正常情况都是打开终端，执行命令；关闭终端，结束会话。当你使用 `ssh` 远程登录时，如果由于不可抗力因素（比如你女朋友不想让你看电脑，而是陪陪她，然后突然断网...）使得会话中断，再次登录想接着之前的思路，对不起，找不到历史记录了，岂不是尴尬？

`Tmux` 就是为了解决这一痛点，将会话和终端分离。关闭终端后，会话继续保留，下次打开终端，连接即可。

## 2.概念

### 2.1 会话

一个会话可以看做一个进程或者一个服务器，它保留着之前操作的所有信息。

### 2.2 窗口

进入一个会话后，我们可以创建许多窗口。每个窗口就好像给终端创建了 `tab` 页，每一个窗口都是一个终端页面。

### 2.3 面板/窗格

一个窗口，我们可以分为很多个 `Panel` 。就是将一个终端分为上下左右等小窗格。

> 如下图，将一个窗口分为左右两个窗格，左边执行项目，右边查看日志。

![Tmux 展示图](https://gitee.com/Ethanyan/pic_data/raw/master/tmux.png)

## 3.安装

```shell
# Ubuntu 或 Debian
$ sudo apt-get install tmux

# CentOS 或 Fedora
$ sudo yum install tmux

# Mac
$ brew install tmux
```

### 3.1 启动退出

```shell
# 启动
$ tmux

# 退出
$ exit
```

## 4.常用操作命令总结

它有很多的操作命令，与此同时还提供了各种快捷键，可以根据个人喜欢进行选择。一听见要记快捷键或者命令就头疼，有木有？放心，常用的没几个：

### 4.1 会话相关

新建一个命名的会话，比如叫 `ethanyan_test`

```shell
tmux new -s ethanyan_test
```

退出当前会话，但是保留，不杀死会话，为了下次直接连接

```shell
tmux detach
```

下次打开终端窗口，在接入上次会话

```shell
tmux at -t ethanyan_test
```

嫌弃会话的名称太难听了，想改个名字（这次我们使用快捷键方便一点）：

```shell
Ctrl+b $
```

解读：进入会话后，按 `Ctrl + b`，然后再输入 `$` 即可更改名字。（ `$` 就是在英文输入法下，先按 `shift` 再按键盘字母上面一排的数字 `4`）

> 使用快捷键的方式，都是先按 `Ctrl + b`，告诉系统你要输入命令了，就如同 `vim` 中按 `esc` 进入命令模式一样，再输入快捷键。

### 4.2 窗口相关

窗口相关直接使用快捷键就完事了。

按 `Ctrl + b` 之后，再按如下的快捷键：

`c` ：创建一个新窗口

`w` ：列出所有窗口，此时可通过方向键和回车键切换选择

`,` ：重命名当前窗口

`&` ：关闭当前窗口

### 4.3 窗格相关

按 `Ctrl + b` 之后，再按如下的快捷键：

`方向键` ：选择窗格

`q` ：显示窗格编号，然后可以快速按对应数字键选择对应的窗格

`z` ：将当前的窗格全屏或者还原

`%` ：划分为左右两个窗格

`"` ：划分为上下两个窗格

> 以上的快捷键和命令都是最最常用的，其他网上查到的命令不知道也无妨。

## 5.vi 命令记录

如果要到**行首**，可以在命令模式下，直接按数字键 `0`。如果要到**行尾**，按大写的 `A` ，即 `shift + a`

### 5.1 NERDTree 插件的快捷键

`o` : 打开节点或者打开文件（打开文件的时候，光标会跑到文件中）

`go` : 打开节点或者打开文件（打开文件的时候，光标在目录树中）

`p` : 到上级目录

`P` : 到根目录

`？`: 帮助

`q` : 关闭

`K` : 到同级目录的第一个节点

`J` : 到同级目录的最后一个节点

`i` : 水平分割预览

`s `: 垂直分割预览

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***