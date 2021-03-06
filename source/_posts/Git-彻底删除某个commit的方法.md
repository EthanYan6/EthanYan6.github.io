---
title: Git 彻底删除某个commit的方法
date: 2019-10-25 19:15:20
tags: git
categories: git
---

 <center>Author：闫玉良</center> 
如果因为一些原因，需要删除某个错误的  commit ，而且需要干净的操作，彻底让其消失，不留痕迹，该如何操作？

 <!-- more --> 

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

> 我向仓库提交了一个大文件，大约 300M，push 失败（因为 git 最大能提交 100M 文件），删除本地文件不行，尝试过修改配置文件，解除 git 只能提交小于 100M 文件的限制，但是未起作用。只能通过删除包含提交此文件的 commit 解决。

废话少说，直奔主题。

1.首先输入如下命令查看历史提交的 commit：

```shell
git log
```

> 重要的是**记下**要删除的 commit 的**上一条** commit 的 **commit号**。如下图，如果要删除箭头所指的 commit，需要记录红框中的 commit号：

![gitlog](https://github.com/EthanYan6/pic/raw/master/Git-%E5%BD%BB%E5%BA%95%E5%88%A0%E9%99%A4%E6%9F%90%E4%B8%AAcommit%E7%9A%84%E6%96%B9%E6%B3%95/gitlog.png)

2.然后执行如下的命令：

```shell
git rebase -i commit号
```

会出现如下界面：

![gitrebase](https://github.com/EthanYan6/pic/raw/master/Git-%E5%BD%BB%E5%BA%95%E5%88%A0%E9%99%A4%E6%9F%90%E4%B8%AAcommit%E7%9A%84%E6%96%B9%E6%B3%95/gitrebase.png)

3.然后将要删除的 commit号 的前缀 `pick` 改为 `drop`。

4.然后可以通过如下命令再次查看是否已经删除：

```shell
git log
```

5.最后通过如下命令将现在的状态推送到远程仓库即可：

```shell
git push origin HEAD -force
```