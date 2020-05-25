---
title: SQL处理排行榜问题
date: 2020-05-25 15:59:00
tags: sql
categories: sql
---

<center>Author：闫玉良</center>

对现有的数据，根据某一字段进行排名，如果没有 `RANK` 函数，`SQL` 语句怎么写？接下来就分享一道面试题......

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.面试题

现在有一张表，保存着学生的相关信息（`id` 、名字以及分数），如下图所示。请写出 `sql` 语句查询排名第4的学生信息。

<img src="https://gitee.com/Ethanyan/pic_data/raw/master/%E6%8E%92%E5%90%8D%E9%97%AE%E9%A2%981.png" alt="排名问题" style="zoom:50%;" />

## 2.答案

```sql
/*
 进行排名，相同的分数同名次
 */
select *
from (select name,
             score,
            (case when @p=score then @r
                  when @p:=score then @r:=@r+1 end) as stu_rank
        from tb_student, (select @r:=0,@p:=NULL) r
       order by score desc) v_rank
where v_rank.stu_rank=4;
```

> 此语句为小闫参考网络类似案例后所写，可以达到预期效果，但在性能或者复杂度上可能有所欠缺，如果您有更好的方法，请关注公众号「全栈技术精选」后台给我留言吧



## 3.分析

### 3.1 准备数据

在讲解之前，先让我们创建一个测试数据库 `testdb`，然后创建一张测试用表 `tb_student`：

```sql
use testdb;

create table tb_student(
    id int auto_increment primary key,
    name varchar(10),
    score int
);
```

然后按照图片中的信息进行构造数据：

```sql
insert into tb_student(name, score) values('小三', 1000);
insert into tb_student(name, score) values('jack', 99);
insert into tb_student(name, score) values('rose', 10);
insert into tb_student(name, score) values('马蓉', 8);
insert into tb_student(name, score) values('李小璐', 40);
insert into tb_student(name, score) values('fuck', 300);
insert into tb_student(name, score) values('李小璐2', 40);
insert into tb_student(name, score) values('李小璐3', 40);
```

### 3.2 知识点

**赋值号**：`:=`

**条件语句**：`case when`

### 3.3 过程

1.首先需要根据分数进行排名（分数相同的同学，名次应该也是相同的，并列关系）

```sql
select name,
       score,
      (case when @p=score then @r
            when @p:=score then @r:=@r+1 end) as stu_rank
from tb_student, (select @r:=0,@p:=NULL) as r
order by score desc;
```

<img src="https://gitee.com/Ethanyan/pic_data/raw/master/%E6%8E%92%E5%90%8D%E9%97%AE%E9%A2%982.png" style="zoom:50%;" />

2.然后再查出某一名次的学生

```sql
/*
 进行排名，相同的分数应该是同名次
 */
select *
from (select name,
             score,
            (case when @p=score then @r
                  when @p:=score then @r:=@r+1 end) as stu_rank
        from tb_student, (select @r:=0,@p:=NULL) r
       order by score desc) v_rank
where v_rank.stu_rank=4;
```

<img src="https://gitee.com/Ethanyan/pic_data/raw/master/%E6%8E%92%E5%90%8D%E9%97%AE%E9%A2%983.png" style="zoom:50%;" />



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***