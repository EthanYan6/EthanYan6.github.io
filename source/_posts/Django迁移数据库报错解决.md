---
title: Django迁移数据库报错解决
date: 2020-03-12 20:14:29
tags: django
categories: django
keywords: django,mysql,pymysql,报错
---

<center>Author：闫玉良</center>

`Django` 最新版本创建应用后，迁移数据库报错怎么办？

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.环境

`Django3.0.2`：

```shell
(django_dev) > python -m django --version
3.0.2
```

`MySQl8.0.19`：

```shell
Server version: 8.0.19 MySQL Community Server - GPL
```

`Python3.7.6`：

```shell
(django_dev) > python --version
Python 3.7.6
```



## 2.报错

执行命令：

```shell
(django_dev) > python manage.py makemigrations polls
```

报错信息如下：

```shell
django.core.exceptions.ImproperlyConfigured: mysqlclient 1.3.13 or newer is required; you have 0.9.3.
```

根据提示，是数据库 `MySQL` 与 `Django` 版本不匹配，建议我们升级 `mysqlclient`。

首先要说明的是，数据库 `MySQL` 与 `Django` 都是最新版本，不存在需要升级的问题。那么问题就应该出现在连接数据库的模块  `PyMySQL` 上了，但它也是安装的最新版：

```shell
(django_dev) > pip install --upgrade PyMySQL
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Requirement already up-to-date: PyMySQL in /Users/ethanyan/pyenv/django_dev/lib/python3.7/site-packages (0.9.3)
```

> 根据提示，该模块并无任何更新可用。



## 3.解决

既然外部组件并无问题，接下来核查代码层面。可以根据报错提示，进入底层代码逐个文件查找原因，最终定位到如下文件：

```shell
django_dev/lib/python3.7/site-packages/django/db/backends/mysql/base.py
```

> `django_dev` 为项目的虚拟环境。

此文件从第 35 行开始，有如下代码在作怪：

```shell
version = Database.version_info
if version < (1, 3, 13):
    raise ImproperlyConfigured('mysqlclient 1.3.13 or newer is required; you have %s.' % Database.__version__)
```

原来是底层有版本校验！查看其他文件，发现此校验并无关联内容，即可以注释掉，我们进行尝试：

```shell
# version = Database.version_info
# if version < (1, 3, 13):
#     raise ImproperlyConfigured('mysqlclient 1.3.13 or newer is required; you have %s.' % Database.__version__)
```

然后再次执行命令：

```shell
(django_dev) > python manage.py makemigrations polls
Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice
```

执行成功。

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

