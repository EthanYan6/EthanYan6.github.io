---
title: mysql中保存并操作json对象
date: 2020-03-21 11:14:18
tags: sql
categories: sql
keywords: mysql,json,object
---

<center>Author：闫玉良</center>

对于最常使用的数据格式 `json` 而言，数据库 `mysql` 提供了保存及一系列操作方法供大家使用，下面我们就来学习一下吧。

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.数据类型

在 `mysql5.7` 版本之前， `json` 数据一般使用数据类型 `LONGBLOB ` 或者 `LONGTEXT` 存储。后来大家「千呼万唤」官方才正式引入了数据类型 `JSON`。

## 2.创建json类型字段

1) 创建一个测试数据库 `testdb`

2) 切换到此数据库下

3) 创建一张表 `t1`，表中包含一个字段 `jsondoc` ，其字段类型为 `JSON`

4) 查看表的构建信息

```sql
mysql> create database testdb;
Query OK, 1 row affected (0.03 sec)

mysql> use testdb;
Database changed

mysql> create table t1 (jsondoc JSON);
Query OK, 0 rows affected (0.07 sec)

mysql> show create table t1;
+-------+----------------------------------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                                         |
+-------+----------------------------------------------------------------------------------------------------------------------+
| t1    | CREATE TABLE `t1` (
  `jsondoc` json DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci |
+-------+----------------------------------------------------------------------------------------------------------------------+
1 row in set (0.01 sec)

```
## 3.增删改查

### 3.1 新增数据

新增三条测试数据

```sql
insert into t1 values
    ('{"name":"ethanyan","age":25}'),
    ('{"x": 17, "x": "red"}'),
    ('{"x": 17, "x": "red", "x": [3, 5, 7]}');
```
> 当我们插入的 `json` 中包含多个相同字段，表中只会保存最后一个值。可以看下方查询结果。

```sql
mysql> select * from t1;
+---------------------------------+
| jsondoc                         |
+---------------------------------+
| {"age": 25, "name": "ethanyan"} |
| {"x": "red"}                    |
| {"x": [3, 5, 7]}                |
+---------------------------------+
3 rows in set (0.00 sec)
```

> 官方文档提示在 8.0.3 版本中有bug，上述同一 `json` 中相同字段情况，会保存第一个值。小闫使用的版本为8.0.19，此bug已修复，最终效果为保存最后一个值。（按书写的先后顺序确定）

### 3.2 修改数据

#### 3.2.1 案例一

将数据 `{"x": "red"}` 修改为 `{"name": "hanhan", "age": 12}`

```sql
update t1 
    set jsondoc='{"name": "hanhan", "age": 12}' 
    where json_extract(jsondoc,"$.x")="red";
```

1） `json_extract()` 函数可以根据 `JSON Path Syntax` 提取`json` 字符串中所需的键值。第一个参数 `jsondoc` 为 `json` 字符串（此处为字段名）；第二个参数 `$.x` 是  `JSON Path Syntax` （此处的意思为提取键 `x` 的值）。

2) 整体解读：将表 `t1`  满足条件的记录中，字段 `jsondoc` 的值更改为 `{"name": "hanhan", "age": 12}`。条件：提取 `json_doc` 中 `x` 值等于 `red` 的记录。

#### 3.2.2 案例二

将数据 `{"x": [3, 5, 7]} ` 修改为 `{"name": "rose", "age": 18}`

```sql
update t1 
    set jsondoc='{"name": "rose", "age": 18}' 
    where json_extract(jsondoc,"$.x[0]")=3;
```

可以看出使用的函数、语法与案例一中大致相同，不同的是  `JSON Path Syntax`。

1) 如果 `json` 字符串中某一键值为数组，我们可以通过下标索引 `[n]` 的方式提取元素。

> `n` 为下标

2) 整体解读：将表 `t1`  满足条件的记录中，字段 `jsondoc` 的值更改为 `{"name": "rose", "age": 18}`。条件：提取 `json_doc` 中 `x` 所指向数组下标为0的元素值等于3的记录。

### 3.3 查询数据

#### 3.3.1 查询全部

我们可以查询全部数据，看上述操作是否已经修改数据。

```sql
mysql> select * from t1;
+---------------------------------+
| jsondoc                         |
+---------------------------------+
| {"age": 25, "name": "ethanyan"} |
| {"age": 12, "name": "hanhan"}   |
| {"age": 18, "name": "rose"}     |
+---------------------------------+
3 rows in set (0.00 sec)
```

#### 3.3.2 查询json中键值对

需求：查询表中字段 `jsondoc` 中 `name` 的值。

**方式一：** 使用操作符 `->`

```sql
mysql> select jsondoc->"$.name" from t1;
+-------------------+
| jsondoc->"$.name" |
+-------------------+
| "ethanyan"        |
| "hanhan"          |
| "rose"            |
+-------------------+
3 rows in set (0.00 sec)
```

**方式二：**使用操作符 `->>`

```sql
mysql> select jsondoc->>"$.name" from t1;
+--------------------+
| jsondoc->>"$.name" |
+--------------------+
| ethanyan           |
| hanhan             |
| rose               |
+--------------------+
3 rows in set (0.00 sec)
```

**方式三：**使用函数 `json_extract()`

```sql
mysql> select json_extract(jsondoc,'$.name') from t1;
+--------------------------------+
| json_extract(jsondoc,'$.name') |
+--------------------------------+
| "ethanyan"                     |
| "hanhan"                       |
| "rose"                         |
+--------------------------------+
3 rows in set (0.00 sec)
```

**注意：**

1) 可以看出方式一与方式三效果完全一致，方式二结果中没有双引号

2) 操作符 `->` 和 `->>` 的左侧为字段名，右侧为  `JSON Path Syntax` 

### 3.4 删除数据

最后我们删除一条记录：

```sql
delete from t1 where json_extract(jsondoc,"$.name")="rose";
```

## 4.有关函数

大家应该发现增删改查其实最大的不同在于：有很多函数的使用。除了函数 `json_extract()` 外，还有哪些呢？一起来看看。

### 4.1 JSON_TYPE

`JSON_TYPE`函数需要一个 `JSON` 参数，并尝试将其解析为 `JSON` 值。如果有效，则返回值的 `JSON` 类型，否则返回错误。

```sql
mysql> select json_type('{"name":"ethanyan"}');
+----------------------------------+
| json_type('{"name":"ethanyan"}') |
+----------------------------------+
| OBJECT                           |
+----------------------------------+
1 row in set (0.00 sec)

mysql> select json_type('[1,"2"]');
+----------------------+
| json_type('[1,"2"]') |
+----------------------+
| ARRAY                |
+----------------------+
1 row in set (0.00 sec)

mysql> select json_type('"hello"');
+----------------------+
| json_type('"hello"') |
+----------------------+
| STRING               |
+----------------------+
1 row in set (0.00 sec)

mysql> select json_type('hello world');
ERROR 3141 (22032): Invalid JSON text in argument 1 to function json_type: "Invalid value." at position 0.
```

### 4.2 JSON_ARRAY

此函数会将给定的参数，生成一个数组。

```sql
mysql> select json_array('a',1,now());
+----------------------------------------+
| json_array('a',1,now())                |
+----------------------------------------+
| ["a", 1, "2020-03-21 11:50:11.000000"] |
+----------------------------------------+
1 row in set (0.00 sec)
```

### 4.3 JSON_OBJECT

此函数会将给定的参数，按照顺序组合为键值对，打包成 `json` 对象。

```sql
mysql> select json_object('name','ethanyan','age',20);
+-----------------------------------------+
| json_object('name','ethanyan','age',20) |
+-----------------------------------------+
| {"age": 20, "name": "ethanyan"}         |
+-----------------------------------------+
1 row in set (0.00 sec)
```

### 4.4 JSON_MERGE_PRESERVE

该函数会将多个 `json` 对象进行合并。

```sql
mysql> select json_merge_preserve('[1,2]','{"school":"Qinghua"}');
+-----------------------------------------------------+
| json_merge_preserve('[1,2]','{"school":"Qinghua"}') |
+-----------------------------------------------------+
| [1, 2, {"school": "Qinghua"}]                       |
+-----------------------------------------------------+
1 row in set (0.00 sec)
```

### 4.5 JSON_MERGE_PATCH

该函数会将多个 `json` 对象合并，并去重。我们与函数 4.4 进行比对直观感受一下。

```sql
mysql> select
    ->     json_merge_preserve('{"a": 1, "b": 2}', '{"c": 3, "a": 4}', '{"c": 5, "d": 3}') as preserve,
    ->     json_merge_patch('{"a": 3, "b": 2}', '{"c": 3, "a": 4}', '{"c": 5, "d": 3}') as patch\G
*************************** 1. row ***************************
preserve: {"a": [1, 4], "b": 2, "c": [3, 5], "d": 3}
   patch: {"a": 4, "b": 2, "c": 5, "d": 3}
1 row in set (0.00 sec)
```


### 4.6 JSON_VALID

验证是否为有效的 `json`

```sql
mysql> select json_valid('{"name":1}');
+--------------------------+
| json_valid('{"name":1}') |
+--------------------------+
|                        1 |
+--------------------------+
1 row in set (0.00 sec)

mysql> select json_valid('null');
+--------------------+
| json_valid('null') |
+--------------------+
|                  1 |
+--------------------+
1 row in set (0.00 sec)

mysql> select json_valid('NULL');
+--------------------+
| json_valid('NULL') |
+--------------------+
|                  0 |
+--------------------+
1 row in set (0.00 sec)
```

> `json` 中如果为空，可以表示为 `""` 或者 `null` 。请不要写 `NULL` 或者 `Null` ，虽然在 `sql` 语法中可以，但是此数据类型中不行。

### 4.7 JSON_SET

此函数可以替换现有路径的值，并添加不存在的路径的值。

比如有一个变量：

```sql
set @j = '["a", {"b": [true, false]}, [10, 20]]';
```

然后我们使用函数进行操作：

```sql
mysql> select json_set(@j, '$[1].b[0]', 1, '$[2][2]', 2);
+--------------------------------------------+
| json_set(@j, '$[1].b[0]', 1, '$[2][2]', 2) |
+--------------------------------------------+
| ["a", {"b": [1, false]}, [10, 20, 2]]      |
+--------------------------------------------+
1 row in set (0.00 sec)
```

### 4.8 JSON_INSERT

添加一个新的键值对，不会更改已经存在的键值对。

```sql
mysql> select json_insert(@j, '$[1].b[0]', 1, '$[2][2]', 2);
+-----------------------------------------------+
| json_insert(@j, '$[1].b[0]', 1, '$[2][2]', 2) |
+-----------------------------------------------+
| ["a", {"b": [true, false]}, [10, 20, 2]]      |
+-----------------------------------------------+
1 row in set (0.00 sec)
```

### 4.9 JSON_REPLACE

替换已有的键值对，不会添加新的键值对。

```sql
mysql> select json_replace(@j, '$[1].b[0]', 1, '$[2][2]', 2);
+------------------------------------------------+
| json_replace(@j, '$[1].b[0]', 1, '$[2][2]', 2) |
+------------------------------------------------+
| ["a", {"b": [1, false]}, [10, 20]]             |
+------------------------------------------------+
1 row in set (0.00 sec)
```

### 4.10 JSON_REMOVE

接收 `JSON` 文档和一个或多个指定要从文档中删除的值的路径。返回值是原始文档减去该文档内存在的路径所选择的值。也就是删除指定键值对。

```sql
mysql> select json_remove(@j, '$[2]', '$[1].b[1]', '$[1].b[1]');
+---------------------------------------------------+
| json_remove(@j, '$[2]', '$[1].b[1]', '$[1].b[1]') |
+---------------------------------------------------+
| ["a", {"b": [true]}]                              |
+---------------------------------------------------+
1 row in set (0.00 sec)
```

## 5.注意

1) 通过 `JSON_OBJECT` 转换的值字符集为 `utf8mb4`，`collation` 为 `utf8mb4_bin`

2) `utf8mb4` 是现在最新版本 `mysql` 默认的编码格式。

3) 原来 `mysql` 支持的 `UTF-8` 编码最大字符长度为 3 字节，如果遇到 4 字节的宽字符就会插入异常了。最著名的错误便是 `Emoji `表情插入报错，还有很多不常用的汉字，以及任何新增的 `Unicode` 字符报错等等。

> `Emoji` 是一种特殊的 `Unicode` 编码，常见于 `ios` 和 `android` 手机上

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***
