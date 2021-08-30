



# MySQL

[toc]

## MySQL初级

### 基础

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



#### 基本命令

```mysql
create database abc; #创建数据库
show databases; #显示数据库
select database(); #查询当前数据库
use 数据库名; #使用数据库
show tables; #显示表

desc 表名; #显示表结构
show columns from 表名; #与 desc 表名 等价
show create table 表名; #显示创建表对应定义
use information_schema
select * from columns where table_name='表名'; #显示表结构

mysql -h localhost -u root -p123456 -d 数据库名 #直接打开数据库
```



#### 删除表

```mysql
delete from 表名 where xxx;#删除满足条件的行
truncate table 表名;#删除表所有内容，清空表
#delete和truncate区别
1. 自增序列，delete不会重置， truncate会重置
2. 事务，delete可以回滚，truncate不会

#多表删除
delete table1 from table1
inner/left/right join from table2
on table1.col = table2.col
where xxx;
```



#### 复制表

``` mysql
create table new_table like ori_table; # 只复制表的结构
create table new_table select * from ori_table [where xxx]; # 复制表的结构和数据
```



#### 列的增删改

```mysql
alter table book change column publishdate pubDate datetime; #修改列名
alter table book modify column pubdate timestamp; #修改列类型
alter table author add column annual double; #增加列名
alter table author drop column annual; #删除列名
```



### [数据类型](https://www.cnblogs.com/-xlp/p/8617760.html)

> 主要包括以下五大类：
>
> 整数类型：BIT、BOOL、TINY INT、SMALL INT、MEDIUM INT、 INT、 BIG INT
>
> 浮点数类型：FLOAT、DOUBLE、DECIMAL
>
> 字符串类型：CHAR、VARCHAR、TINY TEXT、TEXT、MEDIUM TEXT、LONGTEXT、TINY BLOB、BLOB、MEDIUM BLOB、LONG BLOB
>
> 日期类型：Date、DateTime、TimeStamp、Time、Year
>
> 其他数据类型：BINARY、VARBINARY、ENUM、SET、Geometry、Point、MultiPoint、LineString、MultiLineString、Polygon、GeometryCollection等



INT(n)中n表示显示长度，需要搭配zerofill使用。

| 整型         | 字节 | 范围                                   |
| ------------ | ---- | -------------------------------------- |
| tinyint      | 1    | 有符号：-128~127<br />无符号：0~255    |
| smallint     | 2    | 有符号：-2^15~2^15-1<br />无符号：2^16 |
| mediumint    | 3    | ...                                    |
| int, integer | 4    | ...                                    |
| bigInt       | 8    | ...                                    |

<br/>

M: 整数和小数的位数和

D: 小数点后保留位数

如果超出范围，会插入临界值

| 浮点数类型  | 字节 | 范围 |
| ----------- | ---- | ---- |
| float(M,D)  | 4    |      |
| double(M,D) | 8    |      |

| 定点数类型                             | 字节 | 范围                                                         |
| -------------------------------------- | ---- | ------------------------------------------------------------ |
| DEC(M,D)<br />DECIMAL(M,D) 默认(10, 0) | M+2  | 最大取值范围与double相同，给定decimal的有效取值范围由M和D决定 |



| 字符串类型 | 字节 | 描述                            |
| ---------- | ---- | ------------------------------- |
| char(M)    | M    | 不可变长度 M在0~255之间         |
| varchar(M) | M    | 可变长度 M在0~65535之间         |
| tinytext   |      | 可变长度，最多255个字符         |
| text       |      | 可变长度，最多65535个字符       |
| mediumtext |      | 可变长度，最多2的24次方-1个字符 |
| longtext   |      | 可变长度，最多2的32次方-1个字符 |

二进制数据(`Blob`)

1. `BLOB`和`text`存储方式不同，`TEXT`以文本方式存储，英文存储区分大小写，而`Blob`是以二进制方式存储，不分大小写。

2. `BLOB`存储的数据只能整体读出。 

3. `TEXT`可以指定字符集，`BLOB`不用指定字符集。

| 类型      | 字节 | 描述 |
| --------- | ---- | ---- |
| binary    |      |      |
| varbinary |      |      |
| enum      |      | 枚举 |
| set       |      | 集合 |

| 类型      | 字节 | 描述                                         |
| --------- | ---- | -------------------------------------------- |
| date      |      | 日期                                         |
| datetime  |      | 日期和时间 会受到时区的影响 范围大 1000~9999 |
| timestamp |      | 时间戳 更能反映实际的日期 范围小 1970~2038   |
| time      |      | 时间                                         |
| year      |      | 年                                           |

#### 六大约束

NOT NULL：非空

DEFAULT：默认值

PRIMARY KEY：主键

UNIQUE：唯一

CHECK：检查（MySQL不支持）

FOREIGN KEY：外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值

> student表中有专业id，student表是从表，专业表是主表。

列级约束：都可以写，但是外键约束没有效果

表级约束：除了非空和默认，其他的都支持

```mysql
CREATE TABLE `shop2` (
   `article` int unsigned NOT NULL DEFAULT '0',
   `dealer` char(20) NOT NULL DEFAULT '',
   `price` decimal(16,2) NOT NULL DEFAULT '0.00',
   PRIMARY KEY (`article`,`dealer`),
   constraint fk_shop2_dealer foreign key (dealer) references dealer(dealer)
 )

# constraint 索引名 可以指定对应约束的名字， show index from shop2; 可以查看对应索引。
```

##### 主键(primary key)和唯一(unique)的联系和区别

1. 都可以保证唯一
2. 都允许组合（不推荐）
3. 主键不允许为空，unique允许
4. 一个表中主键只能有一个，unique可以有多个

##### 外键

1. 要求在从表设置外键关系
2. 从表外键列的类型和主表关联列的类型要求一致或兼容，名称无要求
3. 主表的关联列必须是一个key（一般是主键或者唯一列）
4. 插入数据时，应先插入主表，再插入从表，删除反之

##### 标识列 auto_increament

1. 必须作用在key上（unique、primary key）
2. 一张表最多设置一个
3. 类型只能是数值型
4. 可以通过 `set auto_increament=3；`设置步长

### TCL(Transaction Control Language) 事务控制语言

#### 基本概念

一个或一组SQL语句组成一个执行单元，这个执行要么全部执行，要么全部不执行

##### 存储引擎

```mysql
show engines; #查看mysql支持的存储引擎
```

##### 事务的ACID属性

1. 原子性（Atomicity）
2. 一致性（Consistency）
3. 隔离性（Isolation）
4. 持久性（Durability）

##### 事务的创建

隐式事务：事务没有明显的开启和结束的标记，例如insert、update、delete语句。

`show variables like 'autocommit';`可以查看到autocommit默认是开启状态

显式事务：事务具有显式的开启和结束标记

前提：必须先设置自动提交功能为禁用 `set autocommit=0;`

```mysql
# 开启事务
set autocommit = 0;
start transaction; #可选
# 编写事务中的sql语句(select insert update delete)
update xxx;
update xxx;
# 结束事务
commit; #提交事务
rollback; #回滚事务
```

#### 隔离机制

对于同时运行的多个事务，当这些事务访问数据库中相同的数据时，如果没有采取必要的隔离机制，就会导致各种并发问题。

> **脏读:** 对于两个事务T1、T2，T1读取了已经被T2更新但还没有被提交的字段，之后T2**回滚**，则T1读取的内容就是临时且无效的。

> **不可重复读：**对于两个事务T1、T2，T1读取了一个字段，然后T2**更新**了该字段，之后T1再次读取同一个字段，值就不同了。

> **幻读：**对于两个事务T1、T2，T1从一个表中读取了一个字段，然后T2在该表中**插入**了一些新的行，之后T1再次读取同一个表，就会多出几行。

##### 4种事务隔离级别：

| 隔离级别                        | 描述                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| READ UNCOMMITED（读未提交数据） | 允许事务读取未被其他事务提交的变更，脏读、不可重复读、幻读都会出现 |
| READ COMMITED（读已提交数据）   | 只允许事务读取已经被其他事务提交的变更，可以避免脏读，但不可重复读和幻读仍然可能出现 |
| REPEATABLE READ（可重复读）     | 确保事务可以多次从一个字段中读取到相同的值，在这个事务持续期间，禁止其他事务对这个字段进行更新。可以避免脏读和不可重复读，但幻读的问题仍然存在 |
| SERIALIZABLE（串行化）          | 确保事务可以从一个表中读取相同的行，在这个事务持续期间，禁止其他事务对该表执行插入，更新和删除，所有并发问题都可以避免，但是性能十分低下 |

Oracle支持两种事务隔离级别：READ COMMITED（默认），SERIALIZABLE

MySQL支持四种事务隔离级别：默认为 REPEATABLE READ

```mysql
select @@transaction_isolation #查看当前隔离级别
set session transaction isolation level read committed #设置当前session隔离级别
set global transaction isolation level read committed #设置数据库系统的全局隔离级别
```

##### savepoint

```mysql
set autocommit = 0;
start transaction;
delete from account where id = 25;
savepoint a; #设置保存点
delete from account wherer id = 28;
rollback to a; #回滚到保存点
```



### 视图

含义：虚拟表，和普通表一样使用

```mysql
create view my_v1 as
select * from table_a join table_b xxx; #创建视图

create or replace my_v1 as xxx; #创建或替换视图
alter view my_v1 as xxx; #修改视图
drop view my_v1,my_v2; #删除视图
desc my_v1;
show create view my_v1; #查看视图创建语句

# 视图的可更新性和视图中查询的定义有关系，以下类型的视图是不能更新的
1. 包含以下关键字的sql语句：分组函数,distinct,group by,having,union,union all
2. 常量视图
3. select中包含子查询
4. join
5. from一个不能更新的视图
6. where子句的子查询引用了from子句中的表
```

##### 视图和表的区别

|      | 创建语法     | 物理空间占用    | 使用           |
| ---- | ------------ | --------------- | -------------- |
| 视图 | create view  | 只保存了SQL逻辑 | 一般只用于查询 |
| 表   | create table | 保存了数据      | 增删改查       |



### 变量

##### 系统变量

变量由系统提供， 不是用户定义，属于服务器层面。

```mysql
#查看变量
show global|session variables [like '%char%']; 

#查看指定的某个系统变量的值
select @@global|session.系统变量名; 

#系统变量名赋值
set global|session 系统变量名 = 值; 
set global|session.系统变量名 = 值;
```

1. 全局变量
2. 会话变量

##### 自定义变量

1. 用户变量

   变量是用户自定义的，不是系统提供的。声明->赋值->使用。作用域同会话变量。

   ```mysql
   #声明并初始化
   set @用户变量名=值;
   set @用户变量名:=值;
   select @用户变量名:=值;
   #赋值
   set @用户变量名=值;
   set @用户变量名:=值;
   select @用户变量名:=值;
   select 字段 into 变量名 from 表名;
   ```

2. 局部变量

   变量作用域只在定义它的begin end中有效

   ```mysql
   #声明并初始化
   declare 局部变量名 类型 [default 值];
   
   #赋值
   set 局部变量名=值;
   set 局部变量名:=值;
   select @局部变量名:=值;
   select 字段 into 局部变量名 from 表名;
   ```

   

### 存储过程和函数

提高代码的重用性，简化操作，减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

#### 存储过程

含义：一组预先编译好的SQL语句的集合，理解成批处理语句

```mysql
#创建语法
create procedure 存储过程名(参数列表)
begin
		存储过程体（一组合法的SQL语句）
end

1. 参数列表包含三部分：参数模式 参数名 参数类型
	IN stuname varchar(20)
	参数模式：
		IN：该参数可以作为输入
		OUT：该参数可以作为返回值
		INOUT：既可以作为输入，又可以作为返回值
2. 如果存储过程体只有一句话，begin end可以省略
	存储过程体中的每一条SQL语句都要求加分号结尾
	存储过程的结尾可以使用DELIMITTER重新设置 （DELIMITER 结束标记）

# 调用
call 存储过程名(实参列表);
```

#### 函数

```mysql
#创建语法
create function 函数名(参数列表) returns 返回类型
begin
		函数体
end

参数列表包括参数名 参数类型
函数体一定要有return语句，没有会报错
如果函数体只有一句话，可以省略begin end
使用delimiter语句设置结束标记

无参返回
delimiter $
create function myf1() returns int
begin
		declare c int default 0;
		select count(*) into c from employees;
		return c;
end $
select myf1()$
有参有返回
create function myf2(empName varchar(20)) returns double
begin
		set @sal=0;
		select salary where last_name = empName;
		return @sal;
end $
select myf2('k_ing')$
```

### 流程控制

#### 分支结构

##### if函数

if (判断，true返回值，false返回值)

```mysql
select if(1=2, "false", "true");
```

##### case结构

case 变量|表达式|字段
when 要判断的值 then 返回的值1
...
else 要返回的值
end case;

```mysql
delimiter $
create procedure test_case(IN score INT)
begin
	case
    when score >= 90 then select 'A';
    when score >= 80 then select 'B';
    when score <= 60 then select 'C';
    else select 'D';
    end case;
end $
```

##### if 结构

```mysql
delimiter $
create function test_if(score INT) returns char no sql
begin
	if score >= 90 then return 'A';
    elseif score >= 80 then return 'B';
    elseif score >= 60 then return 'C';
    else return 'D';
    end if;
end $
delimiter ;
```

#### 循环结构

##### while （重要）

```mysql
delimiter $
create procedure pro_while2(IN insertCount INT)
begin
	declare i int default 1;
    a:while i<=insertCount do
		insert into test(name, money) values(concat('Leo', i), 100);
		if i>=20 then leave a; [if MOD(i,2)!=0 then iterate a;]
        end if;
        set i=i+1;
	end while;
end $
delimiter ;
```

##### repeat

```mysql
delimiter $
create procedure pro_repeat(IN insertCount INT)
begin
	declare i int default 1;
    a:repeat
		insert into test(name, money) values(concat('Leo', i), 100);
        set i=i+1;
	until i>insertCount
	end repeat a;
end $
delimiter ;
```

##### loop



## MySQL高级

#### MySQL逻辑架构

1. 连接层
2. 服务层
3. 引擎层
4. 存储层

```mysql
show engines;
show variables like '%storage_engine%';
```

| 对比项   | MyISAM                                                   | InnoDB                                                       |
| -------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| 主外键   | 不支持                                                   | 支持                                                         |
| 事务     | 不支持                                                   | 支持                                                         |
| 行表锁   | 表锁，即使操作一条记录也会锁住整个表，不适合高并发的操作 | 行锁，操作是只锁某一行，不对其他行有影响，**适合高并发的操作** |
| 缓存     | 只缓存索引，不缓存真实数据                               | 不仅缓存索引还会缓存真实数据，对内存要求较高，而且内存大小对性能有决定性的影响 |
|          | 小                                                       | 大                                                           |
| 关注点   | 性能                                                     | 事务                                                         |
| 默认安装 | Y                                                        | Y                                                            |

#### SQL语句慢

1. 查询语句写的烂（关联子查询，join的表太多）

2. 索引失效

   1. 单值索引

      `create index idx_user_name on user(name)`

   2. 复合索引

      `create index idx_user_nameEmail on user(name, email)`

      

3. 服务器调优和各个参数配置不合理

##### SQL执行顺序

> 开始->FROM子句->WHERE子句->GROUP BY子句->HAVING子句->ORDER BY子句->SELECT子句->LIMIT子句->最终结果 

https://blog.csdn.net/u014044812/article/details/51004754

### 索引

索引分类：

1. 单值索引（普通索引）：只包含一个列的索引，一个表可以有多个单列索引。
2. 唯一索引：与前面的普通索引类似，不同的就是：索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。
3. 复合索引（组合索引）：指多个字段上创建的索引，只有在查询条件中使用了创建索引时的第一个字段，索引才会被使用。使用组合索引时遵循最左前缀集合。
4. 主键索引：是一种特殊的唯一索引，一个表只能有一个主键，不允许有空值。一般是在建表的时候同时创建主键索引。
5. 全文索引：主要用来查找文本中的关键字，而不是直接与索引中的值相比较。fulltext索引跟其它索引大不相同，它更像是一个搜索引擎，而不是简单的where语句的参数匹配。fulltext索引配合match against操作使用，而不是一般的where语句加like。它可以在create table，alter table ，create index使用，不过目前只有char、varchar，text 列上可以创建全文索引。值得一提的是，在数据量较大时候，现将数据放入一个没有全局索引的表中，然后再用CREATE index创建fulltext索引，要比先为一张表建立fulltext然后再将数据写入的速度快很多。

```mysql
CREATE TABLE table_name[col_name data type]
[unique|fulltext][index|key][index_name](col_name[length])[asc|desc]
1.unique|fulltext为可选参数，分别表示唯一索引、全文索引
2.index和key为同义词，两者作用相同，用来指定创建索引
3.col_name为需要创建索引的字段列，该列必须从数据表中该定义的多个列中选择
4.index_name指定索引的名称，为可选参数，如果不指定，默认col_name为索引值
5.length为可选参数，表示索引的长度，只有字符串类型的字段才能指定索引长度
6.asc或desc指定升序或降序的索引值存储
```

##### B树和B+树，为什么索引选择了B+树



##### 哪些情况需要创建索引

1. 主键自动建立唯一索引
2. 频繁作为查询条件的字段应该创建索引
3. 查询中与其他表关联的字段，外键关系建立索引
4. 查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度
5. 查询中统计或者分组字段

##### 哪些情况不需要创建索引

1. 频繁更新的字段不适合创建索引
2. where条件里用不到的字段不创建索引
3. 表的记录太少
4. 数据重复并且分布平均的表字段，如性别等

##### 创建什么类型的索引

1. 高并发情况下优先选择联合索引



### 性能分析

##### MySQL Query Optimizer

1. MySQL中内置select语句优化器模块，会通过计算分析系统中收集到的统计信息，为客户端请求的Query提供它认为的最优的执行计划

##### MySQL常见瓶颈

1. CPU：CPU在饱和一般发生在数据装入内存或从磁盘中读取数据
2. IO：磁盘I/O瓶颈发生在装入数据远大于内存容量的时候
3. 服务器硬件的性能瓶颈：top，free，iostat和vmstat来查看系统的性能状态

##### [explain关键字](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html)

使用explain关键字可以模拟优化器执行SQL查询语句，从而知道MySQL是如何处理你的SQL语句。分析你的查询语句或是表结构的性能瓶颈。

`explain select * from employees where birth_date > '1950-01-01' order by birth_date;`

+----+-------------+-----------+------------+------+---------------+------+---------+------+--------+----------+-----------------------------+
| id | select_type | table     | partitions | type | possible_keys | key  | key_len | ref  | rows   | filtered | Extra                       |
+----+-------------+-----------+------------+------+---------------+------+---------+------+--------+----------+-----------------------------+
|  1 | SIMPLE      | employees | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 299335 |    33.33 | Using where; Using filesort |
+----+-------------+-----------+------------+------+---------------+------+---------+------+--------+----------+-----------------------------+

1. 表的读取顺序
2. 数据读取操作的操作类型
3. 哪些索引可以使用
4. 哪些索引被实际使用
5. 表之间的引用
6. 每张表有多少行被优化器查询

**explain结果解释**

| 列名        | 说明                   | 解释                                                         |      |
| ----------- | ---------------------- | ------------------------------------------------------------ | ---- |
| id          | 表的读取顺序           | 按照id从大到小执行，id相同则按照顺序执行                     |      |
| select_type | 数据读取操作的操作类型 | simple(不包含子查询或union)<br />primary(查询中包含任何复杂的子部分，最外层查询则被标记为primary)<br />subquery(子查询)<br />deviced(衍生的表)<br />union， union result |      |
| table       | 操作哪张表             | derived2表示id为2的语句衍生出来的表                          |      |
| type        |                        | 性能由高到低排列：<br />system（表只有一行记录，等于系统表，是const类型的特例，平时不出现）<br />const（通过索引一次就找到了，const用于比较primary key或者unique索引）<br />eq_ref（唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描）<br />ref（非唯一性索引扫描，返回匹配某个单独值的所有行，本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，它可能会找到多个复合条件的行）<br />range（）<br />index<br />ALL |      |





### 锁

锁分类：

1. 表级锁定(table-level)

   开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低；

   使用表级锁定的主要是MyISAM，MEMORY，CSV等一些非事务性存储引擎。

2. 行级锁定(row-level)

   开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高；  

   使用行级锁定的主要是InnoDB存储引擎。

3. 页级锁定(page-level)

   开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之间，并发度一般。

   使用页级锁定的主要是BerkeleyDB存储引擎。

##### 表锁

MySQL表级锁的两种模式：

1. 表共享读锁（Table Read Lock）
2. 表独占写锁（Table Write Lock）

如何加表锁：

MyISAM在执行查询语句（SELECT）前，会自动给涉及的所有表加读锁，在执行更新操作（UPDATE、DELETE、INSERT等）前，会自动给涉及的表加写锁，这个过程并不需要用户干预，因此，用户一般不需要直接用LOCK TABLE命令给MyISAM表显式加锁。

##### 行锁

1. InnoDB锁定模式及实现机制

   行级锁：共享锁，排它锁

   意向锁（表级锁）：意向共享锁，意向排它锁

   ![img](https://images2015.cnblogs.com/blog/1033231/201701/1033231-20170118181153984-1417117507.png)

```mysql
共享锁（S）：SELECT * FROM table_name WHERE ... LOCK IN SHARE MODE
排他锁（X)：SELECT * FROM table_name WHERE ... FOR UPDATE
```





