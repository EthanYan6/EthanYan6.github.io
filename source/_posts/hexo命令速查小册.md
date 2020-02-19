---
title: hexo命令速查小册
date: 2019-10-27 19:41:36
tags:
- hexo
- blog
- command
categories:
- hexo
---

 <center>Author：闫玉良</center> 
最近都在搭建自己的博客，不断的调整显示效果，以及添加一些初始功能，总算大体框架完成，细枝末节尚待商榷。小闫同学的博客地址为  http://www.pythonnote.cn/  ，希望大家多多捧场 ~ 当然也希望大家多多关注公众号『全栈技术精选』

先来几张效果图压场，deng ~ deng ~ dengdeng

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

![](https://github.com/EthanYan6/pic/raw/master/hexo%E5%91%BD%E4%BB%A4%E9%80%9F%E6%9F%A5%E5%B0%8F%E5%86%8C/blog1.png)

![](https://github.com/EthanYan6/pic/raw/master/hexo%E5%91%BD%E4%BB%A4%E9%80%9F%E6%9F%A5%E5%B0%8F%E5%86%8C/blog2.png)

![](https://github.com/EthanYan6/pic/raw/master/hexo%E5%91%BD%E4%BB%A4%E9%80%9F%E6%9F%A5%E5%B0%8F%E5%86%8C/blog3.png)


前端超牛逼的效果有没有感动到 ~ 这当然不是我自己做的，还要感谢 Jonathan Klughertz

如果认为这篇文章要教如何搭建个人博客，那就让你失望了，因为百度上面相关教程太多太多，我不认为自己写的比其他人更详细更好。只是给大家指引一下方向即可。

搭建博客的过程中使用到了框架 Node.js、HEXO 以及 GitHub 。

下面就经常使用到的 hexo 的命令做一个总结，方便在使用过程中快速查阅。

#### 创建新文章

```shell
hexo n "article name"
或者
hexo new "article name"
```

#### 快速部署

```shell
hexo clean && hexo g && hexo d
```

其实上述为 3 个命令，为了节省时间，我经常联合使用。

> hexo clean 清除缓存
>
> hexo g 生成静态页面（generate）
>
> hexo d 部署（deploy）

#### 本地效果预览

```shell
hexo s
```

> s 为 server 的简写，因此你也可以写成 hexo server。hexo 的命令大多都是如此

#### 文章摘要

首页会根据分页效果展示文章，如果不设置摘要，文章会拉长页面篇幅。

我们只需要在文章摘要后添加一行内容即可：

```shell
<!--more-->
```

最后效果图为：

![](https://github.com/EthanYan6/pic/raw/master/hexo%E5%91%BD%E4%BB%A4%E9%80%9F%E6%9F%A5%E5%B0%8F%E5%86%8C/blog4.png)

#### 添加标签

先执行：

```shell
hexo new page "tags"
```

再在目录 `source/tags/index.md` 中添加：

```shell
---
title: tags
date: 2019-05-27 13:47:40
type: "tags"
---
```

最后只需要在写文章时添加 `tags` 属性即可。

如果只添加一个标签：

```shell
tags: test
```

多标签，文章开头添加：（yaml语法）

```shell
tags:
- test1
- test2
- test3
```

#### 添加文章分类目录

添加分类如同标签一样，只需要将 `tags` 换成 `categories` 即可，如：

```shell
hexo new page "categories"
```

**注意：**如下形式，同篇文章添加多个目录和标签：

```yml
tags:
- 算法
- 数据结构
categories:
- 算法
- 数据结构
```

**同篇文章添加多个目录和标签时，请将与其他文章共同的目录放置在前面，优先级高一些。**比如，文章A归为目录算法和机器学习下，文章B归为目录算法和数据结构下。他们在划分目录时，需如下填写：

```yaml
# 文章A
categories:
- 算法
- 机器学习

# 文章B
categories:
- 算法
- 数据结构
```

如果先写各自不同的目录，再写共同目录时，会在前端目录展示页面中出现重复目录。因为框架在创建目录时，会按如下形式创建表示目录的文件夹，以上述例子示范：

```yaml
算法
├── 机器学习
└── 数据结构
# 框架会先创建一个算法的文件夹，然后在算法文件夹下创建两个其他文件夹，这样避免目录重复
```

如果在目录填写时按如下情况：

```yaml
# 文章A
categories:
- 机器学习
- 算法

# 文章B
categories:
- 数据结构
- 算法
```

框架在生成目录时：

```yaml
机器学习
└── 算法

数据结构
└── 算法
# 框架会先生成每篇文章中各自不同的目录，然后在其下创建算法目录，造成重复
```

框架在生成目录时，会优先创建写在首位的目录，再创建写在后面为止的目录，所以在展示所有的目录时，会出现重复的两个 `算法` 目录。

#### 文章中插入图片

如果在 Markdown 中直接插图，再转成静态页面时，路径会找不到，导致图片无法正常显示。解决办法有很多，我最喜欢的便是利用 GitHub 新建一个放置图片的仓库（当然很多人使用七牛云也很方便），然后在文章中使用插入连接的方式插入图片即可：

```shell
![](https://github.com/EthanYan6/pic/raw/master/hexo%E5%91%BD%E4%BB%A4%E9%80%9F%E6%9F%A5%E5%B0%8F%E5%86%8C/blog1.png)
```

> 连接地址利用以下步骤获取：打开GitHub图片仓库后，看到 Download 按钮，右键复制链接

#### 绑定域名

大家首先需要去腾讯云或者阿里云购买一个域名，然后备案，再通过解析绑定个人博客地址。百度教程超详细，不过多阐述

命令暂且总结到此，预知后事如何，且听下回分解 ~