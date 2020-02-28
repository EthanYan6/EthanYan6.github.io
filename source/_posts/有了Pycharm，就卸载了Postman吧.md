---
title: 有了Pycharm，就卸载了Postman吧
date: 2020-02-28 20:04:12
tags:
- pycharm
- postman
categories:
- pycharm
---

 <center>Author：闫玉良</center> 

`Web` 开发还在一手 `PyCharm` 一手 `Postman` 吗？快给电脑减负（卸载掉 `Postman`）吧！教你如何使用 `PyCharm` 替代 `Postman` 。

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

在 `PyCharm` 的 `Professional`  版本中，有一个专门测试 `RESTful` 接口的工具「`HTTP client`」。也许你从未听说过，但是看了这篇文章后你将发现一个新世界。

## 1.窗口化操作

如果你习惯了 `Postman` ，无法突然适应发生的变化，可以采用此种方法。

### 1.1 步骤

1) 点击顶部菜单栏中的 「`Tools`」 按钮

2) 点击下拉菜单中的 「`HTTP client`」

3) 点击菜单中的「`Test RESTful Web Service`」

![HTTP client](https://gitee.com/Ethanyan/pic_data/raw/master/httpclient.png)

**下面是界面介绍：**

![HTTP client提示](https://gitee.com/Ethanyan/pic_data/raw/master/httpclientdesc.png)

## 2.curl 操作

如果你是一个 `Linux` 习惯者，可以在黑窗口中来去自如，那么完全可以使用此种方法。

### 2.1 步骤

1) 点击顶部菜单栏中的 「`Tools`」 按钮

2) 点击下拉菜单中的 「`HTTP client`」

3) 点击菜单中的「`Convert cURL to HTTP Request`」

![HTTP client2](https://gitee.com/Ethanyan/pic_data/raw/master/curl.png)

4) 然后你会看到如下界面，再按要求填写完成之后，点击「`CONVERT`」进行转换。转换后会发现进入了一个脚本文件（详细请看后面内容介绍），点击 `url` 左侧的运行绿色按钮即可执行访问。

![HTTP client2提示](https://gitee.com/Ethanyan/pic_data/raw/master/curldesc.png)

## 3.编写脚本文件

如果是最新版本的 `PyCharm` ，官方强烈推荐你使用最新的方式，即类似于脚本的方法，直接编写访问用例。语法超级简单，就好像写接口文档一样。界面超级清爽，以下将会以常见的场景讲解使用方法。

### 3.1 步骤

1) 首先创建3个文件：`http-client.env.json`(此文件为配置文件，文件名固定)、`http-client.private.env.json`(此文件为配置文件，文件名固定)、`xxx.http`(此为脚本文件，`xxx` 为自定义名称)

2) 配置环境文件 `http-client.env.json`。此文件是通用配置文件，比如有两套环境（开发和测试），可以使用如下的方式进行配置，在使用时随意切换：

```json
{
    "development": {
        "host": "192.168.0.88",
        "id-value": 1,
        "username": "",
        "password": "",
        "my-var": "my-dev-value"
    },

    "test": {
        "host": "192.168.0.99",
        "id-value": 2,
        "username": "",
        "password": "",
        "my-var": "my-test-value"
    }
}
```

> 1.配置文件中的字段可以自定义，相当于变量。脚本文件中使用变量的语法是 `{{字段名}}`。字段名便是环境配置文件中定义的字段。
>
> 2.也许你好奇为什么 `username` 和 `password` 两个字段空着。那是因为如此私密的信息当然要保存在私密文件中啦 ~ 此时就用到了下一个文件 `http-client.private.env.json`

3) 配置私密环境文件  `http-client.private.env.json` 。此文件中保存账号密码等信息：

```json
{
    "development": {
        "username": "admin",
        "password": "admin"
    },

    "test": {
        "username": "admin",
        "password": "admin"
    }
}
```

4) 编写脚本文件。

> 如果没有变量，没有账号密码等私密信息，完全可以省略上面的步骤，直接编写访问用例

语法如下：

```http
# 此处是注释信息，采用井号
# 访问方式，格式：请求方式 url
GET http://{{host}}/es/stage_order_info/_search
# 请求头,注意：请求头与url之间不许有空行
Accept: application/json
# 引用变量的方法：{{xxx}}
Authorization: Basic {{username}} {{password}}
...
# 请求体，如果有的话
...
# 最后以三个井号结尾

###
```

### 3.2 示例

1) `GET` 方式请求：

```http
GET http://{{host}}/es/stage_order_info/_search
Accept: application/json
Authorization: Basic {{username}} {{password}}

###
```

2) `DELETE` 方式请求：

```http
DELETE http://{{host}}/es/ethanyantest
Authorization: Basic {{username}} {{password}}

###
```

3) `PUT` 方式请求：

```http
PUT http://{{host}}/es/ethanyantest/_doc/1
Authorization: Basic {{username}} {{password}}
Content-Type: application/json

{"testfield": "测试成功"}

###
```

4) `POST` 方式请求：

```http
POST http://{{host}}/es/ethanyantest/_doc/1/_update
Authorization: Basic {{username}} {{password}}
Content-Type: application/json

{
   "doc" : {
      "testfield" : [ "{{testfield}}" ],
      "views": 0
   }
}

###
```

### 3.3 运行方式

1.点击请求地址前的运行按钮：

![运行方式1](https://gitee.com/Ethanyan/pic_data/raw/master/%E8%BF%90%E8%A1%8C1.png)

2.选择运行环境：

![运行方式1](https://gitee.com/Ethanyan/pic_data/raw/master/image-20200228213716238.png)

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

