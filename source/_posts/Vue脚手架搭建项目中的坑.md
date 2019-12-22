---
title: Vue脚手架搭建项目中的坑
date: 2019-12-15 18:43:13
tags:
- 前端
- vue
categories:
- 前端
---

<center>Author:闫玉良</center>
`Vue` 框架如同 `Python` 中的 `Django` 框架一样，让一切操作变得超级简单，只需在规定的目录下书写规定的代码即可，至于如何运作，框架自己搞定。此篇文章仅用于记录 `Vue` 项目中的一些小坑。

<!--more-->

我发现 `Vue` 这个框架真的超级严格，不按规定书写代码，就报错给你看！这与 `Python` 不同，在 `Python` 中大多数人都会遵循 `PEP8` ，但毕竟只是规范，除了一些中大型企业会去 `review` 代码，小型企业都是随缘，全凭编程人员习惯。

虽然动不动就报错，但是好在报错信息超级丰富，可快速定位，除了逻辑问题，其他问题均可快速定位解决。

#### 1.空行坑

在 `Vue` 中，每个文件代码都**必须**以空行结尾！否则提醒你：

```shell
Newline required at end of file but not found
# 结尾未看到规定的空行
```

注意，还只能是一行，要不然会提示：

```shell
Too many blank lines at the end of file. Max of 0 allowed
# 结尾的空行太多了，超出最大限制
```

#### 2.字符串的坑

在 `Python` 中字符串，可以是单引号、双引号，还可以是三引号。但如果在你的 `js` 文件中向这样写道：

```js
export default {
  name: "CopClass",
  data () {
    return {
      msg: '今晚吃什么'
    }
  }
}
```

它会提示：

```shell
  ✘  http://eslint.org/docs/rules/quotes  Strings must use singlequote
  src/components/EnterPage.vue:13:9
    name: "CopClass",
```

瞧，告诉你字符串必须使用单引号，还指出了具体的位置。

#### 3.空格坑

在 `data` 传递参数 `msg` 时，可要注意了，像下面的几种情况都会报错：

```js
// first
  data() {
    return {
      msg: '今晚吃什么'
    }
  }
// second
  data (){
    return {
      msg: '今晚吃什么'
    }
  }
// third
  data () {
    return{
      msg: '今晚吃什么'
    }
  }
```

都会及时告诉你某某处缺少空格，请核查。





