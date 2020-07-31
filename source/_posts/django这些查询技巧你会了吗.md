---
title: django这些查询技巧你会了吗?
tags: django
categories: django
date: 2020-07-31 16:13:49
---
<center>Author：闫玉良</center>

任何不考虑后果的查询都是耍流氓！

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.select_related

> 关联查询优化，只会进行**一次**查询，然后缓存结果。平时其他方法都是惰性查询，当每次取值时都会查询一次数据库。
>
> **建议**：所有关联查询使用此方法。

假设有两张表，一张表是书籍，一张表是英雄人物：

```python
from django.db import models


# 书籍表
class BookInfo(models.Model):
    btitle = models.CharField(max_length=20, verbose_name='名称')

    class Meta:
        db_table = 'tb_books'
        

# 英雄表
class HeroInfo(models.Model):
    GENDER_CHOICES = (
        (0, 'male'),
        (1, 'female')
    )
    hname = models.CharField(max_length=20, verbose_name='名称')
    hgender = models.SmallIntegerField(choices=GENDER_CHOICES, default=0, verbose_name='性别')
    hbook = models.ForeignKey(BookInfo, on_delete=models.CASCADE, verbose_name='图书')

    class Meta:
        db_table = 'tb_heros'
```

现在要查询所有的英雄以及他们所在的书籍名称：

```python
heros = HeroInfo.objects.select_related('hbook').all()
# 查询之后构造一个字典
heros_dict = {}
for hero in heros:
    heros_dict[hero.hname] = hero.hbook.btitle  # 关联查询后，直接使用.取值即可，不会再进行数据库查询
```

> 1.如上 `select_related` 中的参数 `hbook` 为外键名称，它会将所关联的表中所有信息都查询出来。
>
> 如果书籍表 `BookInfo` 中还有外键，我们还想再关联查询那张表的信息，可以用如下的方式：`hbook__外键名称`，外键和外键之间用双下划线连接。如果有三层关系可以：`外键名称__外键名称__外键名称`，以此类推，快乐就完事儿了。
>
> 3.`select_related` 中还可以通过参数 `depth` 指定查询的深度。比如示例中可以改为：`select_related(depth=1)`，它的意思就是往下查询一层。如果书籍表中还有外键，我们都想查询出来，可以：`select_related(depth=2)`，以此类推。
>
> 4.示例中的方式是指定查询的外键，只查询了英雄类中所关联的图书。如果英雄类中有好几个外键，我们都想关联查询，参数 `depth` 的优势就凸显出来了，不需要详细写出每一个外键的名称。

最后的结果形如：

```python
{
  '迪迦': '迪迦奥特曼',
  '武松': '水浒传',
  ...
}
```

## 2.values_list

> 取出指定字段，组成列表

比如我需要一个所有英雄名称的列表：`['迪迦', '武松'...]`。你可能想到的方法是查询出所有的英雄信息，然后遍历每个对象，将对象的名称放到指定的列表中，完成需求。使用 `values_list` 可以一步到位：

```python
list(HeroInfo.objects.values_list('hname', flat=True))
```

### 2.1 详解

1.`values_list` 中第一个参数填写的是要查询的字段名称，此处为 `hname` ；第二个参数 `flat` 设置为 `True` 是为了不以元祖的形式展示，而是直接将值取出来，形如：

```python
<QuerySet ['迪迦', '武松'...]>
# 不设置 flat 结果如下
<QuerySet [('迪迦',), ('武松',)...]>
```

2.有人会问：为什么外面要使用 `list` 方法呢？如上示例，查询结果是一个 `QuerySet` 对象，为了使用方便，一般根据需要转换为 `python` 的列表。

## 3.annotate

> 聚合函数

**需求**：统计出英雄表中男女的数量，显示聚合结果。

```python
from django.db.models import Count

HeroInfo.objects.values('hgender').annotate(number=Count('hgender'))
```

### 3.1 详解

1.上方的意思是根据字段 `hgender` 进行分组，然后统计每组的人数，人数一列起名为 `number`

2.查询结果如下：

```python
<QuerySet [{'hgender': 0, 'number': 10}, {'hgender': 1, 'number': 3}]>
```

3.可以看出英雄表中男性（0）为 10 人；女性（1）为 3 人。

4.**注意**：`values` 和 `annotate` 的顺序一定要特别关注！示例中的顺序代表根据性别分组显示聚合结果，但是反过来后就变成了先根据 `hgender` 统计数量，最后只取字段 `hgender` 的值。

> 也许你会想示例中既然  `annotate` 可以统计数量，那么直接使用去掉 `values` 不是好了？不对！它是统计了数量，但只是为查询的每行记录赋予了一个新字段 `number` ，并没有聚合结果，不如示例中那样清晰，哪个性别有几个，展示的明明白白。

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

