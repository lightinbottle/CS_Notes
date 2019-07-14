

# MySQL基础

## 1. Mysql基础语法

* `mysql -u root -p` 登陆

* `show databases` 显示所有表

* `use [database name]`切换数据库

* `select database()`显示当前使用的数据库

* `show tables`显示当前数据库的所有表

* `show columns from [table name]`显示数据表的属性，属性类型，主键信息 ，是否为 NULL，默认值等其他信息。 

* `show index from [table name]`显示数据表的详细索引信息，包括PRIMARY KEY（主键）。 

* `show status from [database name] like ['pattern%'] \G` 该命令将输出Mysql数据库管理系统的性能及统计信息。   '\G ':按列展示

* `create database [database name]`创建database

* `drop database [database name]`创建database

* `create table if not exists [table name](column_name clomun_type) ` 创建数据表

* `drop table [table name]`删除表

* `INSERT INTO table_name ( field1, field2,...fieldN )   VALUES   (value1, value2,...valueN )`插入数据

* `select * from [table name] `读取数据表的所有值

* `select [column_name,column_name] from [table_name] [WHERE Clause][LIMIT N][ OFFSET M]`查询数据表

  * 你可以使用 WHERE 语句来包含任何条件。
  * 你可以使用 LIMIT 属性来设定返回的记录数。
  * 你可以通过OFFSET指定SELECT语句开始查询的数据偏移量。默认情况下偏移量为0。

* `select [table_name1.field1, table_name2.field2,...fieldN] from [table_name1, table_name2...] [where condition1 [and [or]] condition2.....`

  * 查询语句中你可以使用一个或者多个表，表之间使用逗号, 分割，并使用WHERE语句来设定查询条件。
  * 你可以在 WHERE 子句中指定任何条件。
  * 你可以使用 AND 或者 OR 指定一个或多个条件。
  * WHERE 子句也可以运用于 SQL 的 DELETE 或者 UPDATE 命令。
  * WHERE 子句类似于程序语言中的 if 条件，根据 MySQL 表中的字段值来读取指定的数据。
  * where对于字符串的比较不区分大小写，如果要区分采用`where binary`代替

* `update [table_name] set [field1=new-value1, field2=new-value2] [where Clause]` 修改数据表数据，修改匹配where条件的所有记录

* `delete from [table name] [where Clause]` 数据表删除

  * 如果没有指定 WHERE 子句，MySQL 表中的所有记录将被删除。

* `select [field1, field2,...fieldN]  from [table name] where [field1] like [condition] and/or ...`

  like 用来表示 字段的匹配

  当like条件中不含有**%**时，作用等同于**=**

  **%**类似于正则表达式中的*****一样，可以匹配任意数目的任意字符

* `union`操作符用于将两个`select`语句的结果组合到一个集合中(按照列方向拼接)，默认会删除重复的数据。`union all`不会删除重复的数据

  ```
  SELECT expression1, expression2, ... expression_n
  FROM tables
  [WHERE conditions]
  UNION [ALL | DISTINCT]
  SELECT expression1, expression2, ... expression_n
  FROM tables
  [WHERE conditions];
  ```

* `order by`排序

  ```
  SELECT field1, field2,...fieldN table_name1, table_name2...
  ORDER BY field1, [field2...] [ASC [DESC]]
  ```

  默认是升序排列，DESC表示降序排列

* `group by`支持对数据表的一个或多个列进行分组，并支持在分组的列上进行函数操作：

  常见的函数包括： `count()统计记录总条数  avg([field name])平均数 sum（）总和  `

  ```
  select column_name,function(column_name)
  from table_name
  where column_name operator value
  group by column_name;
  ```

  * group by 对多个列进行分组时，只有两个列值都相同的记录才会被视为同一组
  * `WITH ROLLUP` 不仅仅能像一般的 GROUP BY 语句那样检索出各组的聚合信息，还能检索出本组类的整体聚合信息。 

* 连接用于两个或两个以上的数据表中操作

  * **INNER JOIN（或JOIN, 内连接,或等值连接）**：获取两个表中字段匹配关系的记录。

  * **LEFT JOIN（左连接）：**获取左表所有记录，即使右表没有对应匹配的记录。

  * **RIGHT JOIN（右连接）：** 与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录。

  * 用法： 

    `mysql> SELECT a.runoob_id, a.runoob_author, b.runoob_count FROM runoob_tbl a INNER JOIN tcount_tbl b ON a.runoob_author = b.runoob_author; `

* 针对`null`的操作符

  * **IS NULL:** 当列的值是 NULL,此运算符返回 true。不支持**=**
  * **IS NOT NULL:** 当列的值不为 NULL, 运算符返回 true。不支持**！=**
  * **<=>:** 比较操作符（不同于=运算符），当比较的的两个值为 NULL 时返回 true。

* Mysql事务

  多个数据库语句组成一个事务，事务具有以下几个特点：

  * **原子性：**一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
  * **一致性：**在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
  * **隔离性：**数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
  * **持久性：**事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

* 事务的控制语句

  * BEGIN或START TRANSACTION；显式地开启一个事务；
  * COMMIT；也可以使用COMMIT WORK，不过二者是等价的。COMMIT会提交事务，并使已对数据库进行的所有修改成为永久性的；
  * ROLLBACK；有可以使用ROLLBACK WORK，不过二者是等价的。回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；
  * SAVEPOINT identifier；SAVEPOINT允许在事务中创建一个保存点，一个事务中可以有多个SAVEPOINT；
  * RELEASE SAVEPOINT identifier；删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常；
  * ROLLBACK TO identifier；把事务回滚到标记点；
  * SET TRANSACTION；用来设置事务的隔离级别。InnoDB存储引擎提供事务的隔离级别有READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ和SERIALIZABLE。

* 