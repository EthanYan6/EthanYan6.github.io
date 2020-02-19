---
title: python导入导出pip环境中所有模块
date: 2019-10-28 23:24:27
tags:
- python
categories:
- python
---

 <center>Author：闫玉良</center> 
刚入职接手项目，python 环境如何搭建？项目依赖模块如何安装？备份环境时如何将所有 pip 安装的模块导出？请看下文

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

导出所有模块：

```shell
pip freeze >requirements.txt
```

导入所有模块：

```shell
pip install -r requirements.txt
```

> **注意**：执行此命令时**需要联网**，其原理还是依次对文件中每一个模块进行 `pip install`