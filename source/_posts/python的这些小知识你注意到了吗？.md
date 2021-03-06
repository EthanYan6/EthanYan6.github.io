---
title: python的这些小知识你注意到了吗？
date: 2020-03-10 16:07:41
tags: python
categories: python
keywords: python,open,asyncio,wraps
---

<center>Author：闫玉良</center>

本文将对一些小知识进行汇总，这些知识也许你用了 `python` 很久，但仍然没注意到。

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.操作文件的 x 模式

对文件进行操作的 `opend` 函数大家都很熟悉，但是你注意到它还有一种模式为 `x` 吗？

今天闲的无聊，翻了翻 `python` 的文档（小闫电脑保存的 `python` 文档为3.8.2版本），然后发现：不光有 `a` 、`w`、`b` 、`r` 、`+` 模式，竟然还有一种模式为 `x`，下面讲一下吧。

官方文档称：在 3.3 版本中，增加了 `x` 模式，它表示创建一个文件，如果文件已经存在，会报错 `FileExistsError` 。还需要注意一点，在 3.3 版本以后，曾经的 `IOError` 被别名为 `OSError`。

## 2.协程库 asyncio

之前常用第三方库 `greentlet` 和 `gevent` 来创建协程，后来在 3.4 版本后，`python` 自身引入了一个协程库 `asyncio` 。它用来实现异步 `I/O`。下面看一下示例代码： 

```python
import asyncio

async def main_one():
    print('hello ...')
    await asyncio.sleep(3)
    print('... main_one!')

async def main_two():
    print('hello ...')
    await asyncio.sleep(3)
    print('... main_two!')

# 创建 task 列表。ensure_future() 函数会返回 task 对象
tasks = [asyncio.ensure_future(main_one()),asyncio.ensure_future(main_two())]
# 使用 get_event_loop() 方法创建一个事件循环
loop = asyncio.get_event_loop()
# 多个任务（即 task 列表）使用 wait() 方法执行
# 然后再调用 run_until_complete() 函数将协程注册到事件循环中
loop.run_until_complete(asyncio.wait(tasks))
```

结果为：

```python
hello ...
hello ...
... main_one!
... main_two!
```

## 3.装饰器 wraps

模块 `functools` 中的 `wraps` ，会让使用了装饰器的函数伪装的更像原函数，也就是将原函数的属性赋值给使用了装饰器后的原函数。这句话可能比较难理解，我们先来回顾一下什么是装饰器？**装饰器就是在不改变现有函数基础上，为函数增加功能的一个函数，它使用闭包来实现。闭包的规则为在函数内部定义了一个函数，内部函数使用了外部函数的变量，外部函数返回了内部函数的引用。**有一点需要注意：虽然被装饰器装饰的函数仍然可以使用原名称调用，但是其实质上指向了装饰器的内函数。通过以下示例进行说明：

1) 先来一个普通的装饰器使用示例

```python
# 定义一个装饰器
def eg_decorator(func):
    def wrapper(*args, **kwds):
        print('我是装饰器啊...')
        return func(*args, **kwds)
    return wrapper

# 使用定义的装饰器装饰函数
@eg_decorator
def noname():
    print('我是示例函数噻...')

# 调用函数
noname()
# 打印函数的 __name__ 属性
print(noname.__name__)

"""
结果是：
我是装饰器啊...
我是示例函数噻...
wrapper
"""
```

可以看到函数 `noname()` 的属性 `__name__` 的值为 `wrapper` ，即函数 `noname()` 指向的是装饰器的内函数。

2) 接下来请出今天的主角 `wraps`：

```python
from functools import wraps  # <-- 导入模块

# 定义一个装饰器
def eg_decorator(func):
    @wraps(func)    # <-- 使用装饰器
    def wrapper(*args, **kwds):
        print('我是装饰器啊...')
        return func(*args, **kwds)
    return wrapper

# 使用定义的装饰器装饰函数
@eg_decorator
def noname():
    print('我是示例函数噻...')

# 调用函数
noname()
# 打印函数的 __name__ 属性
print(noname.__name__)

"""
结果是：
我是装饰器啊...
我是示例函数噻...
noname
"""
```

可以看到 `__name__` 属性的值被修改为函数 `noname` 自己。

也许大家会问这有什么用？很久很久以前，在一个 `Flask` 项目中曾经出现过一个 `bug` ：给视图函数添加了自定义的装饰器之后（该装饰器会验证用户是否登录），再用 `@app.route("/index")` 绑定路径，会报错「你添加路径的视图函数已经绑定了其他路径」。这是因为路径会绑定自定义装饰器的内函数，之前其他视图绑定的其实并不是视图本身，而是自定义装饰器的内函数，这次实质上也去绑定内函数，所以会报错。通过查看底层代码是因为 `Flask` 的路由地址根据属性 `__name__` 绑定视图名称。

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***