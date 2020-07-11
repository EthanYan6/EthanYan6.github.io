---
title: js动态绑定事件，无法使用for循环中变量i的问题
tags: js
categories: js
date: 2020-07-11 16:44:23
---

<center>Author：闫玉良</center>

每天不是在写 `bug`，就是在解 `bug` 的路上~

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.问题描述

在一个 `for` 循环中，我动态给一堆 `a` 标签绑定 `onclick` 事件时，发现点击事件不正确。如下代码：

```js
var link = document.getElementsByClassName("link");
for (var i = 0; i < link.length; i++) {
  link[i].onclick = function() {
    alert(i);
  };
}
```

我需要的是，点击第一个链接，弹出 0，点击第二个链接，弹出 1......依次类推。但是上述代码点击每一个链接总是弹出一个值，而且还是个不正常的值。之所以说它不正常，是因为上面我获取到了 5 个标签，正常下标应该到 4 结束，但是总是弹出 5 ？？？？

答：那是因为事件函数是一个匿名函数，此函数会在 `for` 循环执行之后调用。调用时，发现内部没有定义变量 `i` ，所以就去外面找一下，发现外层有，就取外层的值了，但是为什么是 5 呢？那是因为 `for` 循环的结束条件是 `i` ，不满足 `i<5` ，那么结束后变量 `i` 的值就是 5，匿名函数到外层取值正好取到了它。

## 2.解决方法

可以采用自定义属性的方式：

```js
var link = document.getElementsByClassName("link");
for (var i = 0; i < link.length; i++) {
  link[i].index = i;
  link[i].onclick = function() {
    alert(this.index);
  };
}
};
//微信公众号：全栈技术精选
```



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***