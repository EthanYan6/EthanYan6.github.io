---
title: js中带有参数的函数作为值传入后调用问题
tags: js
categories: js
date: 2020-07-11 17:33:56
---

<center>Author：闫玉良</center>

每天不是在写 `bug`，就是在解 `bug` 的路上~

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.无参数函数作为参数传入调用

当根据实际情况，函数需要作为参数传入时，一般采用如下方式直接调用即可：

```js
function fuc1() {
  console.log(1);
}
function fuc2(a) {
  a();
}
fuc2(fuc1); // 1
```

## 2.有参数函数作为参数传入调用

一般的函数都有参数，那么这种情况又如何传参呢？可以使用如下方式：

```js
function fuc1(param) {
  console.log(param);
}
function fuc2(a, b) {
  a(b);
}
fuc2(fuc1, "欢迎关注微信公众号：全栈技术精选");
```

## 3.有参数函数作为事件方法

现在要将传入的函数作为点击事件的处理程序，你一定想得是这样：

```js
function fuc1(param) {
  alert(param);
}
var link = document.getElementsByClassName("link1");
link.onclick = fuc1("我是小闫同学啊");
```

但是不好意思，**不需要点击，一刷新页面，直接调用函数**，弹出窗口！

> 因为在你写 fuc1("我是小闫同学啊") 时，默认就调用了此函数，都不需要点击。

如何才能达到在点击时才弹出窗口呢？你需要这样做：

```js
//请关注：全栈技术精选
function fuc1(param) {
  alert(param);
}
var link = document.getElementById("link1");
link.onclick = function() {
  fuc1("我是小闫同学啊");
  return false;
};
```

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

