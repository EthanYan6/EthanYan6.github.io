---
title: 'Mac用户注意了,你的sed命令'
date: 2020-06-13 12:31:55
tags:
- MacOS
- sed
categories:
- Mac
---

<center>Author：闫玉良</center>

在 `MacOS` 上使用 `sed` 命令时与 `linux` 上并不相同，一定要主要！

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

当我们在使用 `sed` 对文本内容进行替换操作时（比如进行全局替换），一般如下：

```shell
sed -i "s#err#right#g" test_sed.txt
```

> 将 `test_sed.txt` 文件中的 `err` 全部替换为 `right`

但是在 `MacOS` 上输出上面的命令，会报错如下：

```shell
sed: 1: "test_sed.txt": undefined label 'est_sed.txt'
```

**原因**是：在 `MacOS` 上使用 `sed` 命令时参数不同

**使用方法**：要达到与上述命令相同结果，只需像下面这样：

```shell
sed -i '' "s#err#right#g" test_sed.txt
```

> 第一个参数为是否需要备份，如果填空不进行备份；一旦填入内容，将生成一个以填入内容为后缀的备份文件。

如要生成备份文件，命令如下：

```shell
sed -i '.bak' "s#right#err#g" test_sed.txt
```

会生成一个 `test_sed.txt.bak` 文件，此文件内容没有更改，`test_sed.txt` 文件内容中所有的 `right` 被更改为 `err`

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***