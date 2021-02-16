---
title: 小闫同学的IT日常-根据外键倒查关联表
tags: 小闫同学的IT日常
categories: 小闫同学的IT日常
date: 2021-02-16 20:05:33
---

当数据库关系十分复杂时，如何快速找出引用了用户表的所有数据表？通过 ER/EER 图查找？太麻烦了吧...

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

小王：“小闫同学，求助！！”

小闫同学：“ 曰【yuē】~”

小王：“现在遇到一个问题：由于业务要求，需要查找出引用 xx 表的全部数据库表”

小闫同学：“你尝试了哪些方法？”

小王：“刚开始先回忆了一番，但是发现关系找不全；然后又通过数据库工具生成了 EER 图，但是看的头都大了也没有结果。”

小闫同学：“没有考虑过代码？比如 python 或者 SQL？”

小王：“想了，但是奈何自身技术受限呀”（摊手表示无奈）

小闫同学：“哈哈哈，书到用时方恨少吧？平时让你好好学的。”

小王：“先别数落我了，救人一命胜造七级浮屠呀！”

小闫同学：“好啦，不开玩笑了，给你这个 SQL 语句去用吧。用完之后，记得分析一下，研究透，变成自己的”

小王：“好嘞！谢谢闫哥”

```sql
select
TABLE_NAME,COLUMN_NAME,CONSTRAINT_NAME, REFERENCED_TABLE_NAME,REFERENCED_COLUMN_NAME
from INFORMATION_SCHEMA.KEY_COLUMN_USAGE
where
REFERENCED_TABLE_NAME = '表名';
```



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***