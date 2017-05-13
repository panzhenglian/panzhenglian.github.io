---
title: mysql去除重复数据
date: 2016-06-25 23:59:12
tags: mysql
---

由于在建表时没有设置唯一键，导致数据有重复，删除方法有很多，这里列出一种简单的方式，通俗易懂，适合新手使用
使用前记得备份。

可以创建一个新表，将原表中重复数据最小的id存储起来，然后删除在原表中所有不在新表中存在的id

代码如下,我需要去除重复数据的表为 detail，判断重复依据的列分别是 listId 和 image
```sql
create table tmp_table (
id int unsigned not null primary key
);
```
然后
```sql
insert into tmp_table (select min(id) from detail group by listId,image);
```
最后
```sql
delete from detail where id not in (select id from tmp_table);
```

大功告成
