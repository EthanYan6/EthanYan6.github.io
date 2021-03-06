---
title: 常用 es 查询盘点
date: 2019-10-25 20:18:57
tags: elasticsearch
categories: elasticsearch
---

 <center>Author：闫玉良</center> 
熟悉了关系型数据库的结构概念以及查询，再去使用属于非关系型数据库的 Elasticsearch 时，简直是噩梦。第一道难关便是概念上的转换。索引？类型？文档？what？

 <!-- more --> 

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

> 下面是概念上的对应，大家可以类比了解

```shell
Relational DB -> Databases -> Tables -> Rows -> Columns
Elasticsearch -> Indices   -> Types  -> Documents -> Fields
```

熟悉了各种概念后，下一道难关便是增删改查，而最最常用的是**查询**！如同 SQL 一样，它有一套自己的查询语句，称为 DSL。所以，下面为大家总结一下常用的查询，然后你就可以像翻字典一样，用时瞄两眼了 ~ 嘿嘿，真他娘的是个天才 ... 

### 1.基础的不能再基础查询

**查询所有的索引及容量：**

```http
GET _cat/indices
```

> `GET` 代表请求方式；`_cat/indices` 代表查询部分。访问的 `url` 写全为：
>
> ```http
> localhost:9200/_cat/indices
> ```

**查询某一索引的映射结构：**

```http
GET 索引名/_mapping
```

**查询所有的相同前缀索引：（如以 yan 开头）**

```http
GET yan*/_search
```

**查询所有索引模板：**

```http
GET _template
```

**查询具体索引模板：**

```http
GET _template/模板名
```

**查询集群健康状态：**

```http
GET _cluster/health
```

**查询所有节点：**

```http
GET _cat/nodes
```

**查询索引及分片的分布：**

```http
GET _cat/shards
```

**查询所有插件:**

```http
GET _cat/plugins
```

### 2.有点难度的查询

**查询某个索引的全部数据：**

```http
GET /index/type/_search
{
  "query": {
    "match_all": {}
  }
}
```

> 当然你也可以直接写 URL 查询（默认返回 10 条文档）：
>
> ```http
> localhost:9200/索引名/_search?pretty
> ```
> pretty参数是为了浏览器显示的美观一些。

**根据某一字段精确查询：**

```http
GET /index/type/_search
{
  "query": {
    "term": { "字段名" : "值" }
  }
}
```

**根据某一字段模糊匹配：**

```http
GET /index/type/_search
{
  "query": {
    "match": { "字段名" : "值" }
  }
}
```

**根据某一字段值进行范围查找：**

```http
GET /index/type/_search
{
  "query": {
    "range": {
    	# 字段名: 条件
        "age":{ "gte" : 15 , "lte" : 25 }
    }
  }
}
```

> gte 是大于等于；lte 是小于等于

**根据条件进行过滤查询：**

```http
GET /index/type/_search
{
  "query": {
    "bool": {
      "filter": {
        "term":{"字段":"值"}
      }
    }
  }
}
```

**多条件"或"关系：**

```http
GET /index/type/_search
{
	"query": {
		"bool": {
			"should": [{
				"term": {
					"字段": "值"
				}
			}, {
				"match": {
					"字段": "值"
				}
			}]
		}
	}
}
```

**多条件"与"关系：**

```http
GET /index/type/_search
{
  "query": {
    "bool": {
      "must" : [{
        "match" : {
          "name" : "Ethanyan"
        }
      },{
        "range":{
        "age":{
          "from" : 18 , "to" : 26
        } 
        }
      }]
    }
  }
}
```

**严格匹配：**

```http
GET /index/type/_search
{
  "query": {
    "bool": {
      "must" : {
        "range" : {
          "age" : { "from" : 18, "to" : 26 }
        }
      }
    }
  }
}
```

**严格不匹配：**

```http
GET /index/type/_search
{
  "query": {
    "bool": {
      "must_not" : {
        "term" : {
          "name" : "Ethanyan"
        }
      }
    }
  }
}
```

**复合查询：**

```http
GET /index/type/_search
{
	"query": {
		"bool": {
			"should": [{
					"match": {
						"age": 18
					}
				},  
				{
					"match": {
						"age": 26
					}
				}
			],
			"filter": { 
				"match": {  
					"name": "Ethanyan" 
				}
			}
		}
	}
}
```

### 3.注意事项

1.索引名、模板名之类的名称不能出现字母大写

2.在查询时，需要写 DSL 语句，所以需要携带请求体，那么为什么还是 get 请求？

答：原则上 get 请求不能携带请求体，但凡事有例外，试验后确实可以，我也不晓得为什么。下方为官方文档，全为 get 请求：

![esquery](https://github.com/EthanYan6/pic/raw/master/%E5%B8%B8%E7%94%A8-es-%E6%9F%A5%E8%AF%A2%E7%9B%98%E7%82%B9/esquery.png)

