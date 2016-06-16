---
title: sqlite joins 子句结合查询
date: 2016-05-18 00:19:50
tags: 
 - Android
 - sqlite3
---
## Sqlite Joins

SQLite 的 Joins 子句用于结合两个或多个数据库中表的记录。JOIN 是一种通过共同值来结合两个表中字段的手段.
SQL 定义了三种主要类型的连接:
- 交叉连接 - CROSS JOIN
- 内连接 - INNER JOIN
- 外连接 - OUTER JOIN


假设我们有两张表
table1:
```db
ID          wid         type
----------  ----------  ----------
1           11           n
2           11           adj
3           12           n
4           12           adj
5           13           adj
6           13           n
7           14           n
8           14           n

```

table2:
```db
id          emid        title	    
----------  ----------  ----------  
1           1           the process of becoming acid ...	 
2           1           being or containing an acid;...	 
3           1           being sour to the taste	 
4           2           any of various water-soluble...	 
5           2           street name for lysergic acid  
6           3           harsh or corrosive in tone	 
7           3           being sour to the taste	 
8           3           having the characteristics...
9           4           having no hue
10          4           n
```
现在的需求是：查询table1中 wid＝12 的所有数据，并且获取所有 table1.id ＝ table2.emid 的数据；
sqlite语句：
```
select * from table1 join table2 in table1.id=table2.emid where wid = 12 ;
```

