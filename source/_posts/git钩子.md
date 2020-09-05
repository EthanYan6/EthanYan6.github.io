---
title: git钩子
tags: git
categories: git
date: 2020-09-05 16:55:41
---

 <center>Author：闫玉良</center> 

如同 `flask` 框架的请求钩子一样，`git` 也有钩子 (`hook`) 的概念，下面就让我们一起来看看吧。

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.概念

执行某些指定命令前后统一进行的操作。比如我要在每次 `commit` 前核查代码，就可以使用钩子 `pre-commit`。

## 2.分类

1.**客户端钩子**：提交或者合并等操作，在客户端进行的操作。

2.**服务端钩子**：接收推送时在服务器端进行的操作。

## 3.原理

这些钩子本质上是一些**脚本**，在执行特定命令时调用这些脚本以达到目的。

大家可以在任意一个 `git` 管理的仓库下 `.git/hooks` 目录中找到官方示例。

```shell
╰─❯ ls # cd .git/hooks
applypatch-msg.sample     fsmonitor-watchman.sample pre-applypatch.sample     pre-push.sample           pre-receive.sample        update.sample
commit-msg.sample         post-update.sample        pre-commit.sample         pre-rebase.sample         prepare-commit-msg.sample
```

> 当你查看这些示例时，会发现他们其实就是一些 `shell` 脚本，也会包含一些其他语言的代码，如 `perl`。
>
> 语言不限定，你甚至可以使用 `Python` 编写它们。

## 4.使用

如果想体验一下官方示例，可以将官方示例的钩子脚本后缀名删掉，这样它就被激活了。

```shell
mv pre-commit.sample pre-commit
```



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***