---
title: elasticsearch的一些小知识点
date: 2020-01-31 19:28:16
tags: elasticsearch
categories: elasticsearch
---

<center>Author：闫玉良</center>

这个假期有多少人新添了一份职业「云监工」，又有多少人干起了小区保安的工作（统计着进出小区的车辆），还有一些人无聊到数火龙果的「黑芝麻」...... 实不相瞒，我也一样，只不过无聊之际，偶尔看看书。之前对于 elasticsearch 介绍过一些常用的 API ，今天再做一些额外的补充。

> 「云监工」：网络流行词，来源于央视频直播武汉开建的防治传染病医院火神山医院的建设工地，网友们虽然不能到现场出把力，但也给自己加了一个身份：云监工，通过直播镜头去“监督”医院的建设进度。
>
> 小区保安工作：很多人在家无聊，自觉统计着出入小区人员车辆数量，以帮助监督是否有人在疫情期间四处乱逛。
>
> 数火龙果的籽：这个起源于网友晒出的图片，为一个火龙果所有的籽标上了序号。

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.短语搜索

之前向大家介绍了模糊搜索 `match` ，现在又有了很多复杂的需求：确切的匹配若干个单词（同一字段的值中包含多个词）或者短语。这就用到了一个新的方法 `match_phrase`，官方示例如下：

```json
GET /megacorp/employee/_search
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    }
}
```

上述语句查询 `about` 字段中同时包含 `rock` 和 `climbing`（并且是相邻的）的员工记录。`match_phrase` 方法也可以用于匹配**短语(phrases)**

有一些新来的小伙伴，可能对于上面的表达不是十分清楚，下面详细介绍一下。`GET` 表示请求方式，`/megacorp/employee/_search` 表示访问路径 `PATH`，大括号中的内容即 `DSL` 语句。

### 1.1 API 访问方式

#### curl

与 `elasticsearch` 进行交互时，可以通过 `crul` 方式，与其他普通的 `HTTP` 请求相同：

```shell
curl -X<VERB> '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'
```

> **VERB** 表示请求方式：`GET`, `POST`, `PUT`, `HEAD`, `DELETE`
>
> **PROTOCOL** 表示访问协议，如 `HTTP` / `HTTPS` （只有在 `Elasticsearch` 前面有 `https` 代理的时候可用）
>
> **HOST** 表示 `Elasticsearch` 集群中的任何一个节点的主机名，如果是在本地的节点，那么就叫 `localhost`
>
> **PORT** 表示 `Elasticsearch HTTP` 服务所在的端口，默认为 9200
>
> **PATH** 表示 `API` 路径，也就是示例中的 `/megacorp/employee/_search`
>
> **QUERY_STRING** 表示一些可选的查询请求参数，例如 `?pretty` 参数使得返回 `JSON` 数据更加美观易读。
>
> **BODY** 一个 `JSON` 格式的请求主体（如果请求需要的话）

#### postman

大家经常使用到的工具，通过输入请求 `url` 以及根据需要填写 `DSL` 语句做为请求体，进行访问。

#### 其他

任何其他发送 `HTTP` 请求的方式

## 2.搜索结果高亮

故名思义，我们可以将搜索到的文档中匹配到的关键词进行高亮显示。它最大的作用就是醒目，让你一眼发现这个文档被匹配的原因。语句也很简单，只需要在之前的查询语句上增加 `highlight`参数：

```Javascript
GET /megacorp/employee/_search
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    },
    "highlight": {
        "fields" : {
            "about" : {}
        }
    }
}
```

当我们运行这个语句时，会命中与之前相同的结果，但是在返回结果中会有一个新的部分叫做 `highlight`，这里包含了来自`about`字段中的文本，并且用 `<em></em>`来标识匹配到的单词。

```Javascript
{
   ...
   "hits": {
      "total":      1,
      "max_score":  0.23013961,
      "hits": [
         {
            ...
            "_score":         0.23013961,
            "_source": {
               "first_name":  "John",
               "last_name":   "Smith",
               "age":         25,
               "about":       "I love to go rock climbing",
               "interests": [ "sports", "music" ]
            },
            "highlight": {
               "about": [
                  "I love to go <em>rock</em> <em>climbing</em>" <1>
               ]
            }
         }
      ]
   }
}
```

> <1> 原有文本中高亮的片段

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***