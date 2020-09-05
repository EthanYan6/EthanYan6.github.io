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

执行某些命令前后，统一进行的操作。比如我要在每次 `commit` 前核查代码，就可以使用钩子 `pre-commit`。

## 2.分类

1.**客户端钩子**：提交或者合并时，在客户端进行的操作。

2.**服务端钩子**：接收推送时，在服务器端进行的操作。

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
> 语言不限定，甚至可以使用 `Python` 编写它们。

## 4.使用

如果想体验一下官方示例，可以将官方示例的钩子脚本后缀名删掉，这样它就被激活了。

```shell
mv pre-commit.sample pre-commit
```

### 4.1 示例

我希望在每次 `commit` 前输出一句话：

```shell
this is pre-commit hook
```

**步骤：**

1.将官方示例进行备份

```shell
cp pre-commit.sample pre-commit.sample.bak 
```

2.修改 `pre-commit` 钩子脚本

```shell
vim pre-commit.sample

#!/bin/sh
echo 'this is pre-commit hook'
```

3.激活钩子

```shell
mv pre-commit.sample pre-commit
```

4.在仓库做一些改变然后进行提交

```shell
# ... do something
git add .
git commit -m 'test hooks'
```

5.输出信息如下

![git-hooks-pre-commit](https://gitee.com/Ethanyan/pic_data/raw/master/git-hooks-simple.png)

从图中可以看到执行了我们的脚本。

## 5.客户端常用的 hooks

### 5.1 pre-commit

在执行 `git commit` 键入提交信息前运行，如果脚本输出非零值，那么就会停止提交。

**用途**：做初步的 `code review` ，检查代码风格是否符合规范；检查代码是否有多余空白字符等等。

### 5.2 post-commit

在整个提交过程完成后运行。

**用途**：提示信息。

### 5.3 pre-push

在 `git push` 运行期间， 更新了远程引用但尚未传送对象时被调用。 它接受远程分支的名字和位置作为参数，同时从标准输入中读取一系列待更新的引用。 如果脚本输出非零值，那么就会停止推送。

**用途**：在推送开始之前，用它验证对引用的更新操作。

### 5.4 post-merge

在 `git merge` 成功运行后，`post-merge` 钩子会被调用。 

**用途**：可以用它恢复 Git 无法跟踪的工作区数据，比如权限数据。 这个钩子也可以用来验证某些在 Git 控制之外的文件是否存在，这样你就能在工作区改变时，把这些文件复制进来。



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***