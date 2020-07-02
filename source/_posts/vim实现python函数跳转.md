---
title: vim实现python函数跳转
tags: vim
categories: vim
date: 2020-07-02 20:35:29
---

<center>Author：闫玉良</center>

网上很多帖子都是说通过 `ctags` 或者 `ExuberantCtags` 来实现函数跳转，如果你是 `C` 语言开发者，无可厚非，`Python` 怎么办？快来看下面操作吧！

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.步骤

1.下载一个文件

2.使用下载的文件为项目生成 `tags` 文件（里面记录了所有函数、类等位置，做了对应关系）

3.将生成的 `tags` 文件让 `vim` 读取

4.使用快捷键跳转

## 2.详细操作

1.复制然后利用浏览器打开网址：http://svn.python.org/projects/python/trunk/Tools/scripts/ptags.py

2.本地新建一个文件 `ptags.py` ，然后把打开网址后看到的内容拷贝进文件中。

3.然后使用如下命令生成 `tags` 文件：

3.1 为当前目录下所有 `python` 程序生成 `tags` 文件

```shell
ptags.py *.py
```

3.2 为整个项目生成 `tags` 文件：1.进入项目根目录；2.执行如下命令：

```shell
find  . -name \*.py -print | xargs /xxx/xxx/ptags.py
```

> 根据 `ptags.py` 文件的位置填写绝对路径

**备注**：如果上述命令执行提示没有权限，不要着急使用 `sudo` ，大概率是你没有给新建的文件 `ptags.py` 赋予执行权限。`chmod 777 文件路径` 暴力赋予一下就好了

4.让 `vim` 找到路径。

4.1 打开配置文件

```shell
vim ~/.vimrc
```

4.2 添加如下内容，然后保存

```shell
set tags+=tags
```

4.3 在命令模式下输入如下命令，重新加载一下 `vim` 配置文件：

```shell
:source $MYVIMRC
```

5.跳转方式：

5.1 移动光标到某函数名下，然后使用快捷键 `ctrl + ]`

5.2 跳转回去：`ctrl + t`

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

