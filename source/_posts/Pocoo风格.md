---
title: Pocoo风格
date: 2020-04-11 19:03:49
tags:
- flask
- pocoo
categories:
- flask
- pocoo
---

 
「无规矩不成方圆」`Python` 官方为所有开发人员制定了一套编码规范 - `PEP 8`。然而大部分研发团队并没有满足于此，就如同 `Flask` 框架的开发团队 `Pocoo` ，基于 `PEP 8` 进行调整扩充，形成了一套新的编码风格。如果你的项目正好使用了此框架，强烈建议仔细阅读。

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.整体布局

1) 缩进采用4个空格，而非制表符（ `Tab` 键）

2) 每行代码的长度不应该超过84个字符，最好保持在79个以内

> 可以通过 `break` 、 `continue` 和 `return ` 等关键字避免层层嵌套，既保持代码的美观，又提升可读性（不必左右拉取进度条）。

3) 如果代码超过如何续行？可以尝试反斜杠 `\`  ，如下方的示例：

```shell
this_is_a_very_long(function_call, 'with many parameters') \
    .that_returns_an_object_with_an_attribute

MyModel.query.filter(MyModel.scalar > 120) \
             .order_by(MyModel.name.desc()) \
             .limit(10)
             
this_is_a_very_long(function_call, 'with many parameters',
                    23, 42, 'and even more')
                    
items = [
    'this is the first', 'set of items', 'with more items',
    'to come in this line', 'like this'
]
```

> 注意：为了保持美观，可以将链式调用中每个方法前的点 `.` 对齐；如果是括号内的换行，请将下一行与上一行的括号对齐，不需要使用续行符号；如果字典、列表、元祖内元素超级多，在起始括号后立即换行。

4) 顶层函数和类由两个空行分隔，其它一个空行。不要使用过多空行来分隔代码逻辑段。例如:

```shell
def hello(name):
    print 'Hello %s!' % name


def goodbye(name):
    print 'See you %s.' % name


class MyClass(object):
    """This is a simple docstring"""

    def __init__(self, name):
        self.name = name

    def get_annoying_name(self):
        return self.name.upper() + '!!!!111'
```

## 2.表达式与代码语句

### 2.1 常规空格规则

1) 不是关键词的一元运算符不使用空格（例如： `- `、 `~` 等等），对于圆括号也是这样。

2) 用空格包围二元运算符。

对:

```shell
exp = -1.05
value = (item_value / item_count) * offset / exp
value = my_list[index]
value = my_dict['key']
```

错:

```shell
exp = - 1.05
value = ( item_value / item_count ) * offset / exp
value = (item_value/item_count)*offset/exp
value=( item_value/item_count ) * offset/exp
value = my_list[ index ]
value = my_dict ['key']
```

### 2.2 禁止 `Yoda` 语句

永远不要用变量来比较常量，而是用常量来比较变量：

对:

```shell
if method == 'md5':
    pass
```

错:

```shell
if 'md5' == method:
    pass
```

### 2.3 比较

1) 针对任意类型使用 `==` 和 `!=`

2) 针对单一类型使用 `is` 和 `is not `（例如： `foo is not None` ）

3) 永远不要与 `True` 或 `False` 作比较（例如永远不要写 `foo == False` ，而应当写 `not foo` ）

### 2.4 排除检验

使用 `foo not in bar` 而不是 `not foo in bar`

### 2.5 实例检验

使用 `isinstance(a, C)` 而不是 `type(A) is C` ，但是通常应当避免检验实例，而应当检验特性。

## 3.命名

1) 类名采用大驼峰： `CamelCase` ，缩写词大写（ `HTTPWriter` 而不是 `HttpWriter`）

2) 变量名采用下划线连接： `lowercase_with_underscores`

3) 方法和函数名采用下划线连接： `lowercase_with_underscores`

4) 常量全部大写并采用下划线连接： `UPPERCASE_WITH_UNDERSCORES`

5) 预编译正则表达式： `name_re`

被保护的成员以单个下划线作为前缀，混合类则使用双下划线。

如果使用关键字作为类的名称，那么在名称末尾添加下划线。与内置构件冲突是允许的，请一定不要用 在变量名后添加下划线 的方式解决冲突。如果函数需要访问一个隐蔽的内置构件，请重新绑定内置构件到一个不同的名字。

### 3.1 函数和方法参数

1) 类方法: `cls` 作为第一个参数

2) 实例方法: `self` 作为第一个参数

3) 用于属性的 `lambda` 表达式应该把第一个参数替换为 `x` ， 像下方代码一样：

```shell
display_name = property(lambda x: x.real_name or x.username)
```

## 4.文档字符串

### 4.1 文档字符串约定

所有的文档字符串为 `Sphinx` 可理解的 `reStructuredText` 格式。它们的形态 因行数不同而不同。如果只有一行，三引号闭合在同一行，否则开头的三引号与文本在同一行，结尾的三引号独立一行:

```shell
def foo():
    """This is a simple docstring"""


def bar():
    """This is a longer docstring with so much information in there
    that it spans three lines.  In this case the closing triple quote
    is on its own line.
    """
```

### 4.2 模块首行

模块首行包含一个 `utf-8` 编码声明（即使没有使用非 `ASCII` 字符，也始终推荐这么做）和一个标准的文档字符串:

```shell
# -*- coding: utf-8 -*-
"""
    package.module
    ~~~~~~~~~~~~~~

    A brief description goes here.

    :copyright: (c) YEAR by AUTHOR.
    :license: LICENSE_NAME, see LICENSE_FILE for more details.
"""
```

谨记使用合适的版权和许可证文件以利于通过 `Flask` 扩展审核。

## 5.注释

注释的规则与文档字符串类似。两者都使用 `reStructuredText` 格式。如果一个 注释被用于一个说明类属性，在起始的井号（ `#` ）后加一个冒号:

```shell
class User(object):
    #: the name of the user as unicode string
    name = Column(String)
    #: the sha1 hash of the password + inline salt
    pw_hash = Column(String)
```

> 翻译自官方文档 https://flask.palletsprojects.com/en/1.0.x/styleguide/

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

