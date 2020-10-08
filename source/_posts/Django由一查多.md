---
title: Django由一查多
tags: django
categories: django
date: 2020-10-08 13:20:51
---

<center>Author：闫玉良</center>

`Django` 由一查多的各种方式。

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

`BookInfo` 是一，`HeroInfo` 是多（`HeroInfo` 定义了外键，对应 `BookInfo`）

### 一、单个对象查询

一对应的模型类对象.多对应的模型类名小写_set

```python
b = BookInfo.objects.get(id=1)
b.heroinfo_set.all()
```

### 二、多个对象同时查询

使用 `values_list` 方法实现 `left join` 效果。

一对应的模型类对象.objects.values_list(要查询的字段名)

```python
BookInfo.objects.values_list('heroinfo__id', flat=True)
```

> 由一查多时，`values_list` 中会有一个字段（该字段是多模型类对象小写名），根据此字段可以查询多对象。如果要查询多对象的某个属性，则用两个下划线链接 `heroinfo__id`

**注意：** 如果列出多个字段时，`flat=True` 属性不能使用，将多个字段同时列出即可。最后的查询结果是 `Queryset` 对象，可以通过工厂方法 `list()` 进行转换。转换后效果是列表套元祖，形如：

```python
[('34143124', '小闫同学'), ('4321443', '小闫同学'), ...]
```

#### 如何将列表套元祖转换为列表套字段的形式

```python
a = ('小闫', 18)
b = ('姓名', '年龄')
dict(zip(b, a))
```

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***