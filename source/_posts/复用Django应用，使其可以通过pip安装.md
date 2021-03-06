---
title: 复用Django应用，使其可以通过pip安装
date: 2020-03-13 19:53:44
tags: django
categories: django
keywords: django,应用,打包,pip
---

<center>Author：闫玉良</center>

想自己打包一个应用，可以像其他模块一样通过 `pip install ` 安装吗？在 `Django` 项目中，一个应用如果通过此方式随用随安装是不是很帅气？快来看一下如何操作吧！

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

# 1.前提

## 1.1应用规范

为了方便打包，你的应用应该解耦合，并且所有文件都规范放置。其中需要注意：按照 `Django3.0` 官方文档描述，每个应用中的模板文件 `templates` 应与主项目中通用模板文件 `templates` 分离，最主要目的是为了创建可复用应用。

> 官方文档原文：Just like the static files, we could have all our templates together, in one big templates directory, and it would work perfectly well. However, templates that belong to a particular application should be placed in that application’s template directory.

**保证应用所需文件全部包含在应用目录中**即可开始操作。

## 1.2 环境

安装模块：

```shell
 pip install setuptools 
 pip install wheel
```



# 2.操作

## 2.1 准备文件

比如我要打包应用 `polls` ：

```shell
polls
├── __init__.py
├── __pycache__
│   ├── __init__.cpython-37.pyc
│   ├── admin.cpython-37.pyc
│   ├── apps.cpython-37.pyc
│   ├── models.cpython-37.pyc
│   ├── tests.cpython-37.pyc
│   ├── urls.cpython-37.pyc
│   ├── views.cpython-37.pyc
│   └── views_bak.cpython-37.pyc
├── admin.py
├── apps.py
├── migrations
│   ├── 0001_initial.py
│   ├── __init__.py
│   └── __pycache__
│       ├── 0001_initial.cpython-37.pyc
│       └── __init__.cpython-37.pyc
├── models.py
├── static
│   └── polls
│       ├── images
│       │   └── 1574779118421.jpg
│       └── style.css
├── templates
│   └── polls
│       ├── details.html
│       ├── index.html
│       └── results.html
├── tests.py
├── urls.py
├── views.py
└── views_bak.py # 视图备份文件
```

### 2.1.1 步骤

#### 2.1.1.1 创建总目录

1) 创建总目录，比如创建目录 `django-polls`

2) 然后将应用 `polls` 移入上方总目录，此时它的目录结构应该是：

```shell
django-polls/polls
```



#### 2.1.1.2 创建说明文档

创建说明文档 `django-polls/README.rst`

```txt
=====
Polls
=====

Polls is a Django app to conduct Web-based polls. For each question,
visitors can choose between a fixed number of answers.

Detailed documentation is in the "docs" directory.

Quick start
-----------

1. Add "polls" to your INSTALLED_APPS setting like this::

    INSTALLED_APPS = [
        ...
        'polls',
    ]

2. Include the polls URLconf in your project urls.py like this::

    path('polls/', include('polls.urls')),

3. Run ``python manage.py migrate`` to create the polls models.

4. Start the development server and visit http://127.0.0.1:8000/admin/
   to create a poll (you'll need the Admin app enabled).

5. Visit http://127.0.0.1:8000/polls/ to participate in the poll.
```



#### 2.1.1.3 创建授权协议文件

创建一个 `django-polls/LICENSE` 文件

```txt
Copyright (c) 2018 The Python Packaging Authority

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

> 协议文件自己可以从网上寻找生成方式



#### 2.1.1.4 创建安装文件

1) 创建文件 `django-polls/setup.cfg` （此文件为模块的配置信息，比如作者的姓名、联系方式等）

```txt
[metadata]
name = django-polls
version = 0.1
description = A Django app to conduct Web-based polls.
long_description = file: README.rst
url = https://www.pythonnote.cn
author = Ethan Yan
author_email = yanyuliang6@163.com
license = MIT  # Example license
classifiers =
    Environment :: Web Environment
    Framework :: Django
    Framework :: Django :: X.Y  # Replace "X.Y" as appropriate
    Intended Audience :: Developers
    License :: OSI Approved :: BSD License
    Operating System :: OS Independent
    Programming Language :: Python
    Programming Language :: Python :: 3
    Programming Language :: Python :: 3 :: Only
    Programming Language :: Python :: 3.6
    Programming Language :: Python :: 3.7
    Programming Language :: Python :: 3.8
    Topic :: Internet :: WWW/HTTP
    Topic :: Internet :: WWW/HTTP :: Dynamic Content

[options]
include_package_data = true
packages = find:
```

2) 创建文件 `django-polls/setup.py` （此文件是为了构建和安装应用）

```txt
from setuptools import setup

setup()
```

#### 2.1.1.5 创建引用文件

默认包中只包含 `Python` 模块和包。为了包含额外文件，我们需要创建一个名为 `MANIFEST.in` 的文件。为了包含模板、`README.rst` 和我们的 `LICENSE` 文件，创建文件 `django-polls/MANIFEST.in` 包含以下内容：

```shell
include LICENSE
include README.rst
recursive-include polls/static *
recursive-include polls/templates *
recursive-include docs *
```

> `docs` 目录为文档存放目录，可有可无，视情况而定。如果需要引入其他文件，按照格式在上述文件中补充即可。

最终的目录结构如下：

```shell
django-polls
├── LICENSE
├── MANIFEST.in
├── README.rst
├── doc
├── polls
├── setup.cfg
└── setup.py
```



## 2.2 打包

接下来快看一下如何打包吧！

1) 进入总目录 `django-polls` 中

2) 执行如下命令：

```shell
python setup.py sdist bdist_wheel
```

3) 最终的模块在如下位置：

```shell
dist
├── django-polls-0.1.tar.gz
└── django_polls-0.1-py3-none-any.whl
```

`django-polls-0.1.tar.gz` 即打包好的应用。

## 2.3 安装使用

在应用包所在目录执行如下命令即可安装：

```shell
python -m pip install django-polls-0.1.tar.gz 
```

安装后如何在 `Django` 项目中使用呢？

1) 在 `settings` 文件中添加如下应用：

```shell
INSTALLED_APPS = [
        ...
        'polls',
    ]
```

2) 在 `urls` 文件中添加如下代码：

```shell
path('polls/', include('polls.urls')),
```

3) 迁移数据库文件，创建 `models`：

```shell
python manage.py migrate
```

然后即可使用。如果想要使用我打包好的应用体验一把，可以在微信公众号「全栈技术精选」后台回复关键字「polls应用包」获取模块。

## 2.4 卸载

卸载：

```shell
python -m pip uninstall django-polls
```



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***