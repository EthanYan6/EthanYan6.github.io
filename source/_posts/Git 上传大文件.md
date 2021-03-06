---
title: Git 上传大文件
date: 2019-10-25 20:18:57
tags: git
categories: git
---

 <center>Author：闫玉良</center> 
我们知道 github 上面限制每次推送的文件不能超过 100M，否则报错失败，那么非要推送大文件呢？有没有什么解决办法呢？答案当然是肯定的。这就用到了 `Git LFS` 。

 <!-- more --> 

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

因为只试验了 windows 平台，本文以此为例。其他系统可上网搜如何下载安装对应版本的 `git-lfs`。

## 1.windows 平台安装 git-lfs

1.先打开网址：

```http
https://github.com/git-lfs/git-lfs/releases
```

2.然后点击最新版本的版本号，将页面拉取到最底部，下载安装：

```shell
Windows Installer
```

3.运行下载的 exe 文件：

```shell
windows installer
```

## 2.使用

1.打开 `git bash`，首先输入下列命令开启 lfs 功能：

```shell
git lfs install
```

2.然后使用如下命令进行大文件追踪，然后会生成文件 `.gitattributes`：

```shell
git lfs track '大文件名'
# 追踪大文件test.tar.gz
git lfs track 'test.tar.gz'
```

3.添加文件 `.gittributes`：

```shell
git add .gitattributes
```

4.然后提交文件 `.gitattributes`：

```shell
git commit -m 'submit .gitattributes file'
```

5.将文件 `.gitattributes` 推送到远程仓库：

```shell
git push -u origin master
```

6.添加大文件：

```shell
git add test.tar.gz
```

7.提交大文件：

```shell
git commit -m 'submit big file.'
```

8.直接推送大文件到远程：

```shell
git push
```

然后你就会发现大文件推送到远程的 github 仓库了。



