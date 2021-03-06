---
title: elasticsearch中join类型数据如何进行父子文档查询
date: 2020-02-07 18:58:35
tags: elasticsearch
categories: elasticsearch
---

 <center>Author：闫玉良</center> 

ES 中 join 类型数据如何根据父文档查询全部子文档？又如何根据子文档查询其父文档呢？

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.join 简介

在 `ES` 中有一种特殊的数据类型『`join`』，被形象地称为父子文档。它是一种可以在同一索引中存放两种有关系数据的数据类型，类似于关系数据库中让两张表发生关系的**外键 `FOREIGN KEY`** 。

在官方文档中这样介绍：`join` 数据类型的字段是一个特殊字段，它可以在同一个索引的文档中创建 `父子关系` 。通过参数 `relations` 定义可能存在关系的一组文档，这个关系的参数由 `父名` 和 `子名` 构成。下方是原文档描述：

The `join` datatype is a special field that creates parent/child relation within documents of the same index. The `relations` section defines a set of possible relations within the documents, each relation being a parent name and a child name. 



## 2.定义

我们需要在设置 `mapping` 时将其关系定义好，如下示例：

```http
PUT 索引名称
{
  "mappings": {
    "properties": {
      "join类型的字段名称": { 
        "type": "join",
        "relations": {
          "父文档标示字段名": "子文档标示字段名" 
        }
      }
    }
  }
}
```



### 2.1 父文档

构建父文档时可以通过如下方法：

```http
PUT 索引名称/类型/文档id?refresh
{
  "text": "EthanYan",
  ...		// 父文档中其他的字段与值
  "join类型的字段名称": {
    "name": "父文档标示字段名" 
  }
}
```

这种方式是为了便于理解，与下方子文档中构建方式对应。当你运用熟练后，有一种简便的构建方法：

```http
PUT 索引名称/类型/文档id?refresh
{
  "text": "EthanYan",
  ...		// 父文档中其他的字段与值
  "join类型的字段名称": "父文档标示字段名" 
}
```



### 2.2 子文档

构建子文档时可以通过如下方法：

```http
PUT 索引名称/类型/文档id?routing=父文档id&refresh 
{
  "text": "xiaoyan",
  ...		// 子文档中其他的字段与值
  "join类型的字段名称": {
    "name": "子文档标示字段名", 
    "parent": "父文档id" 
  }
}
```

#### 注意

构建子文档时与父文档有些许不同，以下几点需要特别注意：

1.`url` 中可以看到有一个参数 `routing` ，**此参数必须设置**，因为我们需要保证父文档与子文档在同一分片中。其原文描述为：The routing value is mandatory because parent and child documents must be indexed on the same shard.

2.我们可以看到子文档在 `join` 类型字段中除了参数 `name` 外，还多了一个参数 `parent` ，故名思义，此字段为了指明父文档的所在，其值填写为父文档的 `id`



## 3.查询

此字段类型当然是为了查询而存在，要不然没有灵魂。下面举例进行说明。索引名为 `sales_org`

有一个父文档为下方示例：

```json
{
  "node_name_cn": "川渝",
  "node_code": "LP.IIB.RW.CTU",
  "node_type": "办事处",
  "node_id_fqdn": "SI/LP/LP.IIB.RW/LP.IIB.RW.CTU",
  "node_name_fqdn": "SI/LP//川渝",
  "node_name_en": "",
  "mgmt_territory": "",
  "node_tree_level": 3,
  "node_name_short": "LP.IIB.RW.CTU",
  "node_info": "node_parent"
}
```

一个子文档示例如下：

```json
{	
  "empl_id": "*******",
  "email_addr": "*****@fafa.com",
  "dept_id": "LP.IIB.RW.CTU",
  "name_cn": "Nie Cong",
  "node_info": {
    "parent": "LP.IIB.RW.CTU",
    "name": "node_child"
  }
}
```

可以看到 `join` 类型字段名为 `node_info` ，父文档标示字段名为 `node_parent` ，子文档标示字段名为 `node_child` 。

### 3.1 基于父文档查询全部子文档

```http
POST sales_org/_search
{
  "query": {
    "has_parent": {
      "parent_type": "node_parent",		// 填写父文档标示字段名
      "query": {		// 填写查询条件，注意填写的查询条件是查询父文档，该查询条件是为定位到要基于的父文档
        "match": {
          "_id": "LP.IIB.RW.CTU"
        }
      }
    }
  }
}
```



### 3.2 基于子文档查询其父文档

```http
{
  "query": {
    "has_child": {
      "type": "node_child",		// 填写子文档标示字段名
      "query": {		// 填写查询条件，注意填写的查询条件是查询子文档，该查询条件是为定位到要基于的子文档
        "match": {
          "dept_id": "LP.IIB.RW.CTU"
        }
      }
    }
  }
}
```



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***