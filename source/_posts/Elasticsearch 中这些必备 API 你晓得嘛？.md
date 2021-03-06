---
title: Elasticsearch 中这些必备 API 你晓得嘛？
date: 2019-10-25 20:18:57
tags: elasticsearch
categories: elasticsearch
---

 <center>Author：闫玉良</center> 


众所周知，ES 中的 mapping 只能在索引建立初期进行创建，且建立之后不可更改。mapping 对应的字段会映射成指定类型，未定义字段则自动映射，一般为 text 类型。那么想更改索引名或者更改某字段的 mapping 时，是否就束手无策了呢？当然不是！！！官方提供了一个接口，供索引备份迁移操作使用，那便是 **reindex**。当旧索引与新索引在同一节点时，效率相当高，万条数据瞬间完成（`大约只需0.4s`）。数百万数据也只需几分钟即可完成 操作，是不是很溜呢？快来学习一下吧。

 <!-- more --> 

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***
## 1.索引备份迁移
**请求方式：POST**

**请求 API `_reindex`**

```json
{
  "source": {
    "index": "要备份的索引名"
  },
  "dest": {
    "index": "新建的索引名"
  }
}
```

> 如果新索引中有数据，且 id 与旧索引中重复，那么会进行覆盖操作。

但是当数据量实在太大，高达几十 G 的时候，速度就会变得很慢，那是因为它底层还是利用了 scroll 操作。该如何进行**优化**呢？可以提升批量操作的文档数大小，如下：

```json
{
  "source": {
    "index": "要备份的索引名",
    "size" : 5000
  },
  "dest": {
    "index"  : "新建的索引名",
    "routing": "=cat"
  }
}
```

批量操作大小是如何确定的呢？是根据文档数以及文档容量进行确定的。根据查阅资料得知，一个好的起点是**每批处理数据 5-15 MB**，这是个什么概念？

> 1000 个 1kb 的文档大约 1MB；1000 个 100kb 的文档是 100MB。

然后**逐步增加每批处理数据容量**，进行调优，直到最终性能无法再次提升。还有一个方法，那便是提升硬件，比如**换固态**，你懂得~

还有一种方法提升写入效率，这是终极操作，那便是 `sliced`。`reindex` 支持 Sliced Scroll 操作。它的原理可以简单的理解为将每次的 Scroll 请求分解，分解后并行化请求，从而提升效率。方式为：

```json
POST _reindex?slices=5&refresh
{
  "source": {
    "index": "要备份的索引名"
  },
  "dest": {
    "index": "新建的索引名"
  }
}
```

## 2.删除索引中指定的数据

**请求方式：POST**

**请求 API `_delete_by_query`**

```json
{
    "query": {
        "match": {
            "字段名": "字段值"
        }
    }
}
```

> 方法便是先查出指定数据再删除。

## 3. 设置副本数、分片、动态映射

设置这些指标项可在建立 mapping 时一同设置。

```json
{
    "settings":{
        "index":{
            // 刷新间隔
            "refresh_interval":"3s"
        },
        // 副本数
        "number_of_replicas":"0",
        // 切片（分布在每个节点上的切片）
        "number_of_shards":"1"
    },
    "mappings":{
        "_doc":{
            // 自动映射为 keyword 类型而不是默认的text类型
            "dynamic_templates":[
                {
                    "string_fields":{
                        "match":"*",
                        "match_mapping_type":"string",
                        "mapping":{
                            "type":"keyword",
                            "norms":false
                        }
                    }
                }
            ],
            // 建立mapping
            "properties":{
                "mydate":{
                    "type":"date",
                    "format":"yyyy/MM/dd HH:mm:ss||date_time||strict_date_time"
                },
                "字段名":{
                    "type":"字段类型"
                }
            }
        }
    }
}
```

