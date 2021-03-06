---
title: git仓库上传了个无法点开的空文件夹是什么鬼？
date: 2020-02-09 23:00:45
tags: git
categories: git
---

 <center>Author：闫玉良</center> 

诡异之事年年有，只是今年有点多，在仓库上传到 GitHub 后，偶然在 GitHub 中瞄了一眼，我的天？这个图标不像文件夹啊？这个怎么点不开？git 上传正常啊？本地工作区和暂存区都是干净的了啊？

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 一、问题描述

本地仓库提交后，`GitHub` 中某文件夹无法正常点击，内容无法显示。

## 二、原因分析

经排查，定位到原因：之前仓库中包含了一个特殊的文件夹（此文件夹是另外一个 `git` 仓库），在执行下面语句时：

```shell
git add .
```

提示仓库中包含其他仓库，需要解决此问题。本人操作是删除掉此文件夹中的 `.git` 配置目录，然后依次执行以下命令：

```shell
git add .
git commit -m 'add the new file'
git push
```

顺利推送仓库到远程，过程中并无报错。这个过程或许是导致问题的原因。

## 三、解决办法

现在问题是本地工作区与暂存区干净如新，远程仓库有问题。该如何解决？这时用到了一个命令：

```shell
git rm --cached <dir_name>
```

我们先来看一下手册：

```shell
usage: git rm [<options>] [--] <file>...

    -n, --dry-run         dry run
    -q, --quiet           do not list removed files
    --cached              only remove from the index
    -f, --force           override the up-to-date check
    -r                    allow recursive removal
    --ignore-unmatch      exit with a zero status even if nothing matched
```

可以看到 `--cached` 的作用：将文件仅仅从索引中移除，翻译成人能看懂的话便是 「之前已经提交，现在将此目录释放掉，恢复到工作区，未被追踪的状态，即 `git add .` 命令之前的状态」

我们执行完此命令之后使用如下命令查看所有改变后的文件状态：

```shell
git status
```

发现出问题的目录已经变成了红色未被追踪的状态，此时将出问题的目录中所有包含 `git` 信息的文件和目录都删除，然后再依次执行命令：

```shell
git add .
git commit -m 'add the new file'
git push
```

去远程仓库中查看后发现问题解决。

## 四、总结

当出现未知问题致使远程仓库同预期结果不一致时，本地可以将问题目录恢复到未被追踪状态，核查完问题之后再次提交最新结果，操作步骤如下：

1.删除缓存，释放目录，恢复到未被追踪状态：

```shell
git rm --cached <dir_name>
```

2.依次执行如下操作再次提交：

```shell
git add .
git commit -m 'Information you need to submit'
git push
```

3.完美解决问题。

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***