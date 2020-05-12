---
title: Markdown语法奇奇怪怪小知识
date: 2020-05-12 17:03:24
tags: markdown
categories: markdown
---

 <center>Author：闫玉良</center> 


`markdown` 语法因为其通用性、简便性广受程序员的喜爱，下面再记录一些语法。

 <!-- more --> 

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.上标/下标

### 示例：

氧气: O<sub>2</sub>

`n` 的平方: n<sup>2</sup>

### 语法：

```markdown
氧气: O<sub>2</sub>
n的平方: n<sup>2</sup>
```

## 2.注脚

### 示例：

![注脚示例](https://gitee.com/Ethanyan/pic_data/raw/master/%E6%B3%A8%E8%84%9A.png)

### 语法：

```markdown
欢迎关注公众号「全栈技术精选[^1]」，它由小闫同学[^2]一手创建，旨在技术共享。

[^1]:全栈技术精选是一个汇集了后端开发、数据分析、爬虫等全方位的技术公众号，干货满满而且免费

[^2]:小闫同学是一名 `python` 开发工程师，也是一名技术文章博主
```

## 3.居中

### 示例：

<center>作者：小闫</center>

### 语法：

```mardown
<center>作者：小闫</center>
```

## 4.流程图

### 示例：

![流程图](https://gitee.com/Ethanyan/pic_data/raw/master/%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

### 语法：

~~~markdown
```flow
st=>start: Start|past:>http://www.google.com[blank]
e=>end: End:>http://www.google.com
op1=>operation: My Operation|past
op2=>operation: Stuff|current
sub1=>subroutine: My Subroutine|invalid
cond=>condition: Yes 
or No?|approved:>http://www.baidu.com
c2=>condition: Good idea|rejected
io=>inputoutput: catch something...|request

st->op1(right)->cond
cond(yes, right)->c2
cond(no)->sub1(left)->op1
c2(yes)->io->e
c2(no)->op2->e
```
~~~

## 5.任务清单

### 示例：

![任务清单](https://gitee.com/Ethanyan/pic_data/raw/master/%E4%BB%BB%E5%8A%A1%E6%B8%85%E5%8D%95.png)

### 语法：

```css
- [ ] 任务一 还没有发表今日文章
- [x] 任务二 收取广告费
```

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***