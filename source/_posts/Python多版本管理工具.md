---
title: Python多版本管理工具
tags: python
categories: python
date: 2020-11-21 17:00:09
---

还在为电脑中只能设置一个 `python`  版本而烦恼吗？还在因为安装了一堆 `python` ，环境混乱而闹心吗？难道就不能在一个 `shell` 中随意切换不同的 `python` 版本吗？当然可以！

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.pyenv - 版本管理神器

>   本文以 macOS 为例，其他系统请查看官网具体操作。

### 1.1 安装

```shell
brew install pyenv
```

然后设置环境变量：

```shell
# pyenv
export PYENV_ROOT=~/.pyenv
export PATH=$PYENV_ROOT/shims:$PATH
```

可以在 `shell` 中直接设置，也可以保存到 `.zshrc` 或 `.bash_profile` 中永久生效。（根据实际情况选择）

### 1.2 基本操作

#### 1.2.1 查看 pyenv 版本

```shell
$ pyenv -v
pyenv 1.2.6
```

#### 1.2.2 查看可安装 python 版本

```shell
$ pyenv install --list
Available versions:
  2.1.3
  2.2.3
  2.3.7
  ...
```

#### 1.2.3 选择版本进行安装

>   此处安装 3.6.8 版本

```shell
$ pyenv install 3.6.8
python-build: use openssl from homebrew
python-build: use readline from homebrew
Downloading Python-3.6.8.tar.xz...
-> https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tar.xz
Installing Python-3.6.8...
python-build: use readline from homebrew
Installed Python-3.6.8 to /Users/ethanyan/.pyenv/versions/3.6.8
```

>   下载时候，最好搭个梯子，你懂得~

#### 1.2.4 查看 pyenv 管理的所有版本

```shell
$ pyenv versions
* system (set by /Users/ethanyan/.pyenv/version)
  3.6.8
  3.6.8/envs/resource_manage
  resource_manage
```

>   其中也会包含创建的虚拟环境，后面会讲到。

#### 1.2.5 切换版本

全局切换

```shell
pyenv global x.x.x
```

当前目录切换

```shell
pyenv local x.x.x
```

解除 `local` 设置

```shell
pyenv local --unset
```

切换为系统版本

```shell
pyenv global system
```

#### 1.2.6 卸载某个版本的 python

```shell
pyenv uninstall x.x.x
```

### 1.3 注意

`pyenv` 的开发团队目前还没有支持 `macOS` 最新系统 `Big Sur`。那我们就用不了了吗？作为程序员，这点困难都解决不了岂不是有点废柴。直接上步骤：

首先直接 `brew install pyenv` ，忽略警告信息。问题出现在 `pyenv install` 后，编译失败。往下进行：

#### 第一步 设置 Xcode

1.打开 `Xcode` 

2.菜单栏中依次选择 `Xcode` >  `Preference` > `Locations`

3.然后设置 `command-line tools` 即可。

#### 第二步 使用如下命令安装 python

>   将其中的 3.6.8 换成任何你需要的版本即可。

```shell
CFLAGS="-I$(brew --prefix openssl)/include -I$(brew --prefix bzip2)/include -I$(brew --prefix readline)/include -I$(xcrun --show-sdk-path)/usr/include" LDFLAGS="-L$(brew --prefix openssl)/lib -L$(brew --prefix readline)/lib -L$(brew --prefix zlib)/lib -L$(brew --prefix bzip2)/lib" pyenv install --patch 3.6.8 < <(curl -sSL https://github.com/python/cpython/commit/8ea6353.patch\?full_index\=1)
```

然后你会发现安装成功了！

## 2.pyenv-virtualenv - 创建不同 python 版本虚拟环境神器

1.安装[pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv)

```shell
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```

2.设置环境变量：

```shell
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

>   可以保存到 `.zshrc` 或 `.bash_profile` 中永久生效。（根据实际情况选择）

3.重新初始化 `shell` 环境：

```shell
exec $SHELL
```

4.创建虚拟环境：

```shell
pyenv virtualenv 你安装的python版本号 虚拟环境名称
```

5.查看所有的虚拟环境：

```shell
pyenv virtualenvs
```

6.激活虚拟环境：

```shell
pyenv activate 虚拟环境名称
```

7.退出虚拟环境：

```shell
pyenv deactivate
```

8.删除虚拟环境：

```shell
pyenv virtualenv-delete 虚拟环境名称
```

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***