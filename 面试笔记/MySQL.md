## MySQL

[toc]

#### value和values的区别

没区别，别名



#### [关联子查询(correlated subquery)](https://www.cnblogs.com/HuZihu/p/12467156.html)<font color='red' size=2> 性能低（不推荐使用） </font>

关联子查询和普通子查询的区别在于：

1. 关联子查询引用了外部查询的列。

2. 执行顺序不同。对于普通子查询，先执行普通子查询，再执行外层查询；而对于关联子查询，先执行外层查询，然后对所有通过过滤条件的记录执行内层查询。

语法：

```SQL
SELECT * FROM t1
  WHERE column1 = ANY (SELECT column1 FROM t2
                       WHERE t2.column2 = t1.column2);
```

在关联子查询中，对于外部查询返回的每一行数据，内部查询都要执行一次。另外，在关联子查询中是信息流是双向的，外部查询的每行数据传递一个值给子查询，然后子查询为每一行数据执行一次并返回它的记录。然后，外部查询根据返回的记录做出决策。

