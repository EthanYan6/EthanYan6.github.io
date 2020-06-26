---
title: Extjs-lesson2
date: 2020-06-26 09:21:31
tags: extjs
categories: extjs
---

<center>Author：闫玉良</center>

`Ext.js` 系列课程笔记「类」

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.定义类

`Ext.js` 是面向对象的，因此我们需要了解一下它的语法，以便后续程序的展开。也许你一直好奇为什么使用该框架经常见到 `Ext`，因为框架中定义了各种类（提供了 300 多个类），不能保证与原生 `js` 或者其他框架没有一点冲突，因此将所有的命名封装到了 `Ext` 中，在此命名空间中就可以为所欲为了。就好比 `JQuery` 中的 `$`。

本来想写 `Ext.define` 的，结果惊奇的发现 `Extjs2.3` 中没有该方法，我也是一脸懵逼。好吧，这一些列文章，我会围绕老版本 `2.3` 进行展开，以适应那些老版本的企业项目。既然如此，那我们就回顾一下 `js` 的面向对象吧。

### 1.1 构造函数模式

> 所谓"构造函数"，其实就是一个普通函数，但是内部使用了`this`变量。对构造函数使用`new`运算符，就能生成实例，并且`this`变量会绑定在实例对象上。

```js
function Father(name, age){
  this.name = name;
  this.age = age;
  this.call = "爸爸"
  this.like = function(){
    alert("藏私房钱~")
  };
}
```

#### 1.1.1 问题

每生成一个实例， `call` 属性和 `like` 方法都会生成相同的内容，这是否太占用空间，浪费内存？那么能否只生成一次，以后的实例都去引用，而非重复内容创建多份呢？当然可以！请看下面：

### 1.2 Prototype模式

昨天的文章 [Extjs-lesson1](https://www.pythonnote.cn/Extjs-lesson1/) 末尾提到了这种方式。我们可以将所有不变的属性以及方法直接定义在 `prototype` 对象上来解决上面提到的问题。

> `Javascript` 规定，每一个构造函数都有一个`prototype`属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。

```js
    function Father(name, age) {
      this.name = name;
      this.age = age;
    }
    Father.prototype.call = "爸爸";
    Father.prototype.like = function() {
      alert("藏私房钱~");
    };
```

## 2.继承

### 2.1 构造函数绑定

> 使用 `apply` 方法，让子类继承父类的方法

```js
    function Father(name, age) {
      this.name = name;
      this.age = age;
      this.call = "爸爸";
      this.like = function() {
        alert("藏私房钱~");
      };
    }
    function Son(name, age, school) {
      Father.apply(this, arguments);  <---- 看这里
      this.name = name;
      this.age = age;
      this.school = school;
    }
		// 创建对象，调用父类方法
    var smallSon = new Son("小明", 6, "庞各庄一高");
    smallSon.like();
```

### 2.2 Prototype模式

```js
    function Father(name, age) {
      this.name = name;
      this.age = age;
    }
    Father.prototype.call = "爸爸";
    Father.prototype.like = function() {
      alert("藏私房钱~");
    };
    function Son(name, age, school) {
      this.name = name;
      this.age = age;
      this.school = school;
    }
    // 将 Son 的 prototype 对象指向一个 Father 示例
    Son.prototype = new Father();
    // 将 constructor 属性，指向它原本的构造函数
    Son.prototype.constructor = Son;
    var smallSon = new Son("小明", 6, "庞各庄一高");
    smallSon.like();
```

> 第一行代码会将原先 `Son` 的 `prototype` 对象重新赋值，导致原先的被删除。任何一个 `prototype` 对象都有一个 `constructor` 属性，指向它的构造函数。经过第一行代码之后， `constructor` 属性也指向了 `Father`，这样一来，所有通过 `Son` 构造的实例的  `constructor` 属性都会指向了 `Father`，继承将完全混乱，所以第二行代码进行拨乱反正。

### 2.3 直接继承prototype

```js
    // 将 Son 的 prototype 对象指向 Father 的 prototype 对象
    Son.prototype = Father.prototype;
    // 将 constructor 属性，指向它原本的构造函数
    Son.prototype.constructor = Son;
```

> **优点** ：效率比较高（不用执行和建立 `Father` 的实例了），比较省内存。
>
> **缺点 ：** `Son.prototype` 和 `Father.prototype` 现在指向了同一个对象，那么任何对 `Son.prototype` 的修改，都会反映到 `Father.prototype`。

#### 2.3.1 改进

```js
    var oEmpty = function() {};
    oEmpty.prototype = Father.prototype;
    Son.prototype = new oEmpty();
    Son.prototype.constructor = Son;
```

> 其实就是利用一个空对象做中介，既节省内存，又不会因为 `Son.prototype` 的修改，反映到 `Father.prototype` 上。

## 3.创建对象

```js
var smallSon = new Son("小明", 6, "庞各庄一高");
```

就是通过 `new` 关键字

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***