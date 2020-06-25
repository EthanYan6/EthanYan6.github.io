---
title: Extjs-lesson1
date: 2020-06-25 15:20:38
tags: extjs
categories: extjs
---

<center>Author：闫玉良</center>

`Ext.js` 系列课程笔记

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.准备工作

1) 熟悉 `HTML`

2) 熟悉 `CSS`

3) 熟练 `javaScript`

4) 熟练  `JS` 的面向对象（`oo`）编程

5) 熟练 `Ajax`

> 书籍推荐：《JavaScript 设计模式》《征服Ajax Web 2.0 开发技术详解》

## 2.Hello World

创建一个 `html` 页面，将 `ext` 的一些必要文件进行引入：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <!-- 引入 css 文件 -->
    <link rel="stylesheet" type="text/css" href="./ext-2.3.0/resources/css/ext-all.css"/>
    <!-- 引入 js 文件 -->
    <script type="text/javascript" src="./ext-2.3.0/adapter/ext/ext-base.js"></script>
    <script type="text/javascript" src="./ext-2.3.0/ext-all-debug.js"></script>
    <!-- 引入自己的 js 文件 -->
    <script type="text/javascript" src="./lesson01.js"></script>
  </head>
  <body></body>
</html>
```

创建对应的 `js` 文件 `lesson01.js` ：

```js
(function() {
  Ext.onReady(function() {
    Ext.MessageBox.alert("提示", "hello world");
  });
})();
```

> 它的入口函数为 `onReady` ，即在加载完成 `html` 页面之后，再加载入口函数的内容。

显示效果：

![ext-弹窗](https://gitee.com/Ethanyan/pic_data/raw/master/%E5%BC%B9%E7%AA%97.png)

## 3.官方 API 文档

小闫同学将开发工程师常用的官方文档进行了汇总，地址为「https://www.pythonnote.cn/OfficialDocuments/」文档会不定期补充更新，请收藏或转发给好友。

> `Ext.js` 的文档也包含在内，点击对应按钮即可跳转。
>
> 包含了英文文档以及中文文档，英文吃力的同学，中文更方便哦 ~

![document](https://gitee.com/Ethanyan/pic_data/raw/master/%E5%AE%98%E6%96%B9%E6%96%87%E6%A1%A3.png)

下面是官方文档界面说明：

![API-zh](https://gitee.com/Ethanyan/pic_data/raw/master/api%20%E8%AF%B4%E6%98%8E.png)

> 文档讲解的超级详细，当哪里不理解的时候，可以快速检索学习。

## 4.对原生 js 扩展

### 4.1 Array remove

`extjs` 对原生的 `js` 进行了一些扩展，使得开发更加的高效化，比如 `Array` 的方法：`remove`。下面是它的 `API`

```shell
remove( o ) : Array
Removes the specified object from the array. If the object is not found nothing happens.

Available since: 1.1.0

Parameters
o : Object
The object to remove

Returns
Array
this array
```

简单的翻译来说：从数组中移除某个指定的对象，如果对象不存在，那么相当于什么都没发生，也不会报错。传入的参数是要移除的对象，返回的是这个数组。

**示例**：

```js
var msgArray = [1, 2, 3, 4, 5];
msgArray.remove(1);
alert(msgArray); // 1 被移除，弹出窗口显示 2,3,4,5
```

> 如果直接在 `js` 代码中执行上述代码会报错：没有 `remove` 方法。而在 `Ext.onReady` 中执行上述代码时，会正常无误。

当然上面只是众多 `API` 中的一个小例子，是为了说明它对原生 `js` 进行了很多扩展。那么他底层是怎么做的呢？

### 4.2 原理

```js
Array.prototype.remove = function(obj) {
  if (null == obj) {
    return false;
  }
  for (var i = 0, n = 0; i < this.length; i++) {
    if (this[i] != obj) {
      this[n++] = this[i];
    }
  }
  this.length -= 1;
};

// 测试
var msgArray = [1, 2, 3, 4, 5];
msgArray.remove(1);
alert(msgArray); // 1 被移除，弹出窗口显示 2,3,4,5
```

其实大部分都是通过上述 `prototype` 为类添加方法，只不过官方的代码逻辑一定比这个完善的多。

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

