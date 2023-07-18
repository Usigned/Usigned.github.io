---
title: SQL in MySQL
tags: [数据库]
toc: true
---

# SQL In MySQL

## 命令行登录

```shell
mysql -u usrname -p pwd -h localhost -P 3306
```

参数	(基本)

- `-u/--user`
- `-p/--password`

可选

- `-h/--host`
- `-P/--port` 

# SQL Syntax

## create/drop创建table/db

```sql
--- create/drop db
create database if not exists db_name;
drop database if exists db_name;

--- create/drop table
create table if not exists table_name
(
  -- c_name [type] [attr1 attr2 ...]
	c_name1 int primary key auto_increment,
  c_name2 varchar(50) not null,
  c_name3 Int default 0 unique
);

drop table if exists table_name;
```

## alter更新表

```sql
alter table table_name
	ADD c_name_new1 [type] not null after c_name1,
	modify c_name_updated [type] [attrs],
	drop c_name;
```

## 添加关系（外键）

```sql
create if not exists table table2
(
  id int primary key,
  table1_id int not null,
  foreign key fk_table1_table2 (table1_id)
  	references table1 (id)
  	on update cascade --- no action / set null / restrict
  	on delete no action
);
```

## 更新主键/外键

```sql
alter table table1
	add primary key (id),
	drop primary key,
	drop foreign key fk_table1_table2,
	add foreign key fk_table1_table2 (table1_id)
  	references table1 (id)
  	on update cascade --- no action / set null / restrict
  	on delete no action;
```

## 字符集

```sql
show charset;
```

> Schema -> inspector

```sql
create/alter database db_name
	character set latin1
```

## SELECT

> sql关键词不区分大小写，且无视空格和换行

### 选择所有属性

```sql
select * 
from ...
```

### select的属性中添加运算

```sql
select point, point *100 + 10, (point + 10) * 100
from ...
```

### as

alias，如果有空格需要用引号

```sql
select 
	point, 
	(point + 10) * 100 as discount
	"abc" as "alias with space char"
from ...
```

> 在后续查询中无法直接使用alias
>
> 可以使用(select aliasName)来替代
>
> ```sql
> select
> 	(point * 0.9) as exp1
> 	(point * 1.1 + 5) as exp2
>   (select exp1 - exp2) as exp3
> from ...
> ```

### distinct

去掉**查询结果**中的重复项

```sql
select distinct
	point, 
	(point + 10) * 100 as discount
from ...
```

## WHERE

### 支持的运算符 

`=/>/</>=/<=/<> or !=`

- 可比较date, int, varchar

```sql
select * 
from usr
where name = 'lzq'
```

### 使用`AND/OR/NOT`组合条件

```sql
SELECT 
	*, 
	quantity * unit_price as total_price 
FROM order_items
WHERE order_id = 6 AND (quantity * unit_price) > 30
-- 无法使用alias做条件
```

### IN

> 语义类似python中的in
>
> 相当于 where 中多个=的语句 + and的语法糖
>
> 典型用例为判断多个离散值，列在小括号的元组中

```sql
select *
from customers
where state NOT IN ("va", "fl", "ga")

---
select * from customers
where state <> "va" and state <> "fl" and state <> "ga"
```

### Between

- BETWEEN `begin`  AND `end`
- begin和end 不能颠倒

>where 中>= + and + <= 的语法糖

```sql
SELECT *
FROM customers
WHERE birth_date BETWEEN "1990-01-01" AND "2000-01-01"
---
SELECT *
FROM customers
WHERE birth_date >= "1990-01-01" AND birth_date <= "2000-01-01"
```

### like(字符串匹配)

> Pattern matching
>
> 感觉不常用，可以被正则表达式完美替代

语法 `attr LIKE template`

1. `%`符号匹配任意长度的字符

example

- `b%`：以`b`开头的
- `%b`：以`b`结尾的
- `%bb%`：含两个连续b的

2. `_`符号匹配**一个字符**

example

- `_y`：第二个字母是y的
- `__y`：第三个字母是y的
- `b_y`：b开头，y结尾，共三个字符长

3. 可以组合NOT

```sql
SELECT *
FROM customers
WHERE 
	(
    address NOT LIKE "%trail%" OR 
	  address LIKE "%avenue%"
  )
  AND phone LIKE "%9"
```

### REGEXP/RLIKE(正则表达式)

语法：`attr (REGEXP|RLIKE) template`

1. 直接使用(包含xx)

   - 语义：匹配所有含字符串的项

   - example

     ```sql
     where last_name regexp 'field'
     --- 等价于 ---
     where last_name rlike 'field'
     --- 等价于 mysql方言---
     where regexp_like(last_name, 'field')
     --- 等价于 ---
     where last_name like '%field%'
     ```

2. `^`以xx开头
   - `'^filed'`：以`filed`开头

3. `$`以xx结尾

   - `'field$'`：以`field$`结尾

4. `|`组合多个pattern

   > 类似或运算

   ```sql
   where last_name regexp '^field|mac|rose'
   ```

   - 组合`^filed`，`mac`，`rose`三个pattern
   - 以field**开头**，或**包含**mac，或**包含**rose

5. `[]`相邻字符组合

   - 括号中包含可能字符
   - 使用[a-z]表示a到z范围

   - example：

     - '[gim]e'：含ge或ie或me

     - 'f[gim]e'：fge/fie/fme

     - [a-h]：表示a到h: abcdefgh

### IS NULL

判读field是否为空

语法：`attr` ` IS` ` NULL`

> 类似python：is None
>
> 差别：无法用=判断NULL

## Order by

查询结果排序

Syntax: `ORDER BY field1 (DESC), field2 (DESC), alias, ...`

- **可以通过alias排序**
- mysql中可以通过**表中字段**而不是结果中字段排序
- 默认升序，通过后序DESC来降序排序

## Limit

限定查询结果个数

> 常用于分页功能实现

syntax

1. LIMIT num
   - 返回前num个结果

2. LIMIT offset, num

   - 跳过前offset个结果，返回num个结果

   - Example

     ```sql
     --- 跳过前6个结果，返回7-9个结果
     select *
     from ...
     LIMIT 6, 3
     ```

## 语句模版

```sql
--- 顺序很重要
SELECT ...
FROM ...
(optional) WHERE ...
(optional) ORDER BY ...
(optional) LIMIT ...
```

## 内连接 INNER JOIN

语法：(INNER) JOIN *tableName* ON *condition*

- JOIN默认INNER JOIN，可以省略inner

Example

```sql
SELECT orders.customer_id
FROM orders
JOIN customers ON orders.customer_id = customers.customer_id
```

- 重名字段必须要显式指明

### 在FROM和JOIN中给表alias

Syntax: 

- `FROM tableName Alias`
- `Join tableName Alias`

语义：

- 和select中使用as不同，此时表的alias可以在任何地方使用，甚至是select中
- **使用alias后，之前的名字无法继续使用**
  - 相当于重命名

example

```
SELECT p.product_id, p.name, oi.quantity, oi.unit_price
FROM products p
JOIN order_items oi
    ON oi.product_id = p.product_id
```

### 跨数据库连接

两张表在不同数据库中时，做join

Syntax : *databaseName*.*TableName*

```sql
SELECT *
FROM database1.a a
JOIN database2.b b
    ON a.id = b.id
```

### 自连接

表和自己做连接

> 典型场景，table中保存id, name和上级的id

方法：**给一个表两个不同的alias，然后做join**

```sql
SELECT e.first_name as "employee", m.first_name as "manager"
FROM employees e
JOIN employees m 
    ON e.reports_to = m.employee_id 
```

### 多表连接

连接多张表

方法：多个JOIN语句

```sql
SELECT *
FROM Table1 t1
JOIN Table2 t2
	ON t1.id = t2.id
JOIN Table3 t3
	ON t1.id= t3.id
...
```

### 复合JOIN语句

有时表会有多个主键(复合主键)

连接复合主键的表

方法：在*condition*中使用AND

```sql
SELECT *
FROM Table1 t1
JOIN Table2 t2
	ON t1.pk1 = t2.pk1 AND t1.pk2 = t2.pk2
```

### MYSQL中隐式JOIN语法

> mysql中的dialect

```sql
--- sql显式join
SELECT *
FROM orders o
JOIN customers c
	ON o.customer_id = c.customer_id

--- mysql中隐式join
SELECT *
FROM orders o, customers c
WHERE o.customer_id = c.customer_id
```

- 建议使用显式join，因为不会忘记join条件

- 隐式join忘记条件时会计算笛卡尔积

  ```sql
  --- 没有join条件，返回笛卡尔积
  SELECT *
  FROM orders o, customers c
  ```

## 外连接 OUTER JOIN

内连接时，只有两个表的字段都存在时才会返回

### LEFT JOIN

Syntax: 

​	FROM table1

​	LEFT (OUTER) JOIN *table2* ON *condition*

语义：*table1*中的所有项都会返回

> OUTER关键字可省略
>
> 和*condition*怎么写的没关系

### **RIGHT JOIN**

Syntax:

​	FROM table1

​	RIGTHT (OUTER) JOIN *table2* ON *condition*

语义：*table2*中所有项都会返回

> LEFT JOIN的语法糖

### 多表外链

方法：使用多个OUTER JOIN

note：为了是代码更可读/可理解，尽量统一使用LEFT JOIN

### 自外连接

方法：同自连接，使用两个alias

## JOIN总结

| 方法       | 语义                                                       | 语法                                                     |
| ---------- | ---------------------------------------------------------- | -------------------------------------------------------- |
| Inner join | 连接两表，只返回join条件成功的项目                         | `JOIN`/`INNER JOIN`                                      |
| Outer join | 连接两表，保留其中一表中所有项目，join条件失败时设置为Null | `LEFT JOIN`: 保留FROM中的表 `RIGHT JOIN`：保留JOIN中的表 |

- Outer join可以视为受限的inner join

- `LEFT`可以视为保留之前的表
- `RIGHT`可以视为保留后面的表

## USING(JOIN语法糖)

如果join的两表中使用**字段名称相同**，可以使用USING代替ON *condition*

```sql
SELECT *
FROM Table1 t1
JOIN Table2 t2
	ON t1.id = t2.id
	
--- using syntaic sugar
SELECT *
FROM Table1 t1
JOIN Table2 t2
	USING id
```

> 只能在字段名称相同情况下使用

## Natural JOIN(mysql语法)

数据库引擎决定 共同的列来join

找不到时则做笛卡尔积

```sql
SELECT *
FROM table1
NATURAL JOIN table2
```

> 由于让数据库引擎决定怎么join，故可能产生意料之外的结果
>
> **不建议使用**

## CROSS JOIN(笛卡尔积)

语法：

1. 显式

   cross join关键字

   ```sql
   SELECT *
   FROM table1
   CROSS JOIN table2
   ```

2. 隐式

   from中列举要cross join的表

   ```sql
   SELECT *
   FROM table1, table2
   ```

语义：两表所有排列组合

## UNION(组合多段查询的结果)

两段结果的行结合

- 两段结果的列数需要相同
- 结果显示为第一段查询的名称

```sql
SELECT col1, col2
FROM table1
UNION
SELECT cola, colb
FROM table2
```

> 典型用例：根据表中值计算结果进行不同的分类处理

## INSERT

Syntax:

```sql
—- Insert a single record 
INSERT INTO customers(first_name, phone, points) 
VALUES (‘Mosh’, NULL, DEFAULT)

—- Insert multiple single records 
INSERT INTO customers(first_name, phone, points) 
VALUES  
    (‘Mosh’, NULL, DEFAULT), 
    (‘Bob’, ‘1234’, 10) 
```

### mysql中嵌套INSERT

使用mysql提供的`LAST_INSERT_ID()`内置函数获取上次生成的id

## 通过查询结果创建表/添加数据

### Create as

```sql
CREATE TABLE tableName AS
--- subquery
SELECT ...
```

- 创建的表有相同的数据类型，但**不会有列属性(PK, NN, UQ, AI等)**

### Insert by subquery

```sql
INSERT INTO tableName
--- subquery
SELECT ...
FROM ...
WHERE ...
```

- subquery

## Update

```sql
UPDATE tableName
SET attr1 = v1, attr2 = v2, ...
WHERE ...
```

### 组合使用subquery

在where中嵌套子查询

```sql
UPDATE orders
SET comments = 'gold custormer'
WHERE customer_id IN
    (
        SELECT customer_id
        FROM customers
        WHERE points > 3000
    );
```

> 单列查询结果等价于元组？

## DELETE

```sql
DELETE FROM tableName
WHERE ...
```

- 不加where会delete所有数据

## 聚合函数

- MAX()
- MIN()
- AVG()
- SUM()
- COUNT()

1. 函数参数可以是值，或者**表达式**
   - `MAX(attr * 1.1)`
   - `COUNT(DISTINCT name)`

2. count(columName)函数中NULL会被跳过，使用count(*)来不跳过

## Group by分组

Syntax

```sql
SELECT ...
FROM ...
WHERE ...
GROUP BY ...
ORDER BY ...
```

- 在where后，order by前

语义

- 结果按照 group by中**属性的所有可能组合**进行分组

- 使用group by后，select/order by中的属性必须具备以下特点之一，否则会报错
  - 在group by属性中
  - 聚合函数的结果且计算**不依赖**于group by中的属性

> Group by应该使用select中除聚合函数外的所有属性

## having分组后筛选

用于处理**group后的数据**

 ```sql
 SELECT ...
 FROM ...
 WHERE ...
 GROUP BY ...
 HAVING condition
 ```

- Having的*condition*中可以使用select中的alias
  - where则不行
- Having中只能使用select返回的属性
  - 原表中的不能使用

> 可以理解为结果处理

## Mysql ROLLUP汇总

mysql中的dialect，用于在group by后对每组数据汇总

Syntax:

```sql
GROUP BY attr1, attr2 WITH ROLLUP
```

语义：对每个attr1中的attr2总结，之后对所有attr1总结

## 将多行查询结果转换为一行

### ALL

Syntax:

```sql
WHERE attr > ALL(1, 2, 3)
```

语义：

- 返回attr大于ALL中**所有值**的结果
  $$
  a > s, \forall e\in \{S\}
  $$

- 一般会结合子查询结果使用

### ANY/SOME

syntanx:

```sql
--- any 和 some 等价
WHERE atrr > ANY(1, 2, 3)
```

语义

- 返回attr大于ALL中**任意**的结果

$$
a>s, \exists e \in \{S\}
$$

- = any 和 in 的语义完全相同

### EXIST

Syntax:

```sql
WHERE EXISTS (
	--- subquery
)
```

语义：

- 只要子查询中产生一条有效结果，就**返回**true
- 有时相比IN有更高的效率，因为无需返回子查询结果
  - in则会执行并返回整个子查询结果
- 常和[相关子查询](#相关子查询)一起使用

## 相关子查询

子查询中用到了上一级查询中的属性

> 有点类似闭包

例子

```sql
SELECT *
FROM	employees e
WHERE salary >
	(
  	SELECT AVG(salary)
    FROM employees
    WHERE office_id = e.office_id
  )
```

- 子查询中用到了上一级查询表中的属性
- 子查询会被调用多次：父查询中每行都会调用一次子查询
- 效率偏低，但是功能强大

## 视图

**创建/替换**

```sql
create or replace view viewName as
(
	--- 子查询
)
```

> 一种保存视图的方式是：将创建视图的语句放在一个文件夹中并加入源码控制中
>
> 或使用show create view查看，但数据库会对原始语句进行修改（不推荐）

**可更新视图**

如过视图建立语句中没有以下函数，则view是可以更新的，即可以进行insert,update,delete操作

- distinct
- 聚合函数
- Group by/having
- union

**防止删除视图中数据**

有时候修改数据之后，数据可能不再符合视图的条件于是就不显示了，此为视图默认行为。

语法：创建视图时添加`with check option`

语义：在试图修改视图中数据时，视图会检查该行为是否会导致视图中数据消失。若会，则组织该操作。

## 查看建表/视图语句

```sql
show create table(view) tablename-or-viewName
```

# 列

## 属性

- PK  - primary key
- NN - not null
- UQ - unique
- AI - auto increment
- default

## 数据类型

- varchar - 变长，设定最长长度
- char - 定长，不足则充填空格至长度
- int - 整形

> 详见[MySQL 数据类型](#MySQL 数据类型)

# MySQL内置函数

## 数值型

- ROUND(*real*, *precision*)
  - Real: 小数
  - Precision: 保留小数位
  - 四舍五入
  - `round(5.75, 1)`返回5.8
  - `round(5.7)`返回6
- TRUNCATE(*real*, *precision*)
  - 截断，不舍入
- CEILING(*real*)
  - 返回大于或等于这个数字的最小整数
  - $\lceil x \rceil$
- FLOOR(*real*)
  - 返回小于于或等于这个数字的最大整数
  - $\lfloor x \rfloor$
- ABS(x)
  - 绝对值
- RAND()
  - 随机 0-1间数

> Mysql numeric functions
>
> [MySQL :: MySQL 8.0 Reference Manual :: 12.6 Numeric Functions and Operators](https://dev.mysql.com/doc/refman/8.0/en/numeric-functions.html)

## 字符串

- length
- upper/lower
- ltrim/rtrim/trim
  - 去空格
- left(string, #chars)/right(string, #chars)
- substring(string, begin, #len)
- Locate(char, string)
  - 位置，不存在返回0
- Replace(string, what-to-replace, substitute)
- Concat(str1, ..., str)

> Msql string functions

## 获取时间

- Now/curdate/curtime
- Year/month/day/hour/minite/second
  - 都会返回整数
- dayname/monthname
  - 返回字符串

- extract

  - SQL标准中一部分，可移植
  - 用于代替year/month/...

  ```sql
  SELECT EXTRACT (YEAR FROM NOW())
  ```

## 格式化日期

date_format(*date*, *fstring*)

- *date*: 日期
- *fstring*: 格式字符串
  - %y：两位数年份
  - %Y：四位数年份

> msql date format string

类似的还有time_format

## 时间运算

- *date_add*(now(), ***interval*** 1 ***year***)

- date_sub 
- date_diff
- time_to_sec
  - 转换为到开始时的秒数

## OTHERS

### 替换查询结果中的空值

**IFNULL**

```sql
select
	IFNULL(shipper_id, 'not assigned') as shipper
from orders
```

**coalesce**

- coalesce(*col1*, *col2*, *col3*, ... *default*)

- col1为空则col2，一直找到不为空的值为止

### IF条件判断

Syntax:

`IF(expression, value1, value2)`

语义：

- expression为真则返回value1，否则value2

Example:

```sql
select
	order_id,
	order_date,
	IF(
  	YEAR(order_date) = YEAR(NOW()),
    'active',
    'archive'
  ) as status
from orders
```

### case分支

Syntax:

```sql
CASE
	WHEN exp1 THEN value1
	WHEN exp2 THEN value2
	...
	ELSE value_default
END
```

# 存储过程

> ~~这部分不知道是mysql方言还是sql标准~~
>
> 部分是mysql方言

## 创建procedure

```sql
create procedure procedure_name()
begin
	statements;
end;
```

## 调用

```sql
call procedure_name()
```

> `delimiter`来改变终止符

## 删除

```sql
drop procedure if exists procedure_name
```

## 使用参数

```sql
create procedure procedure_name(
	param1 [TYPE1],
  param2 [TYPE2],
  ...
)
begin
	[statements];
end
```

- 参数需要指明类型
- 参数和表列名重名时，需要使用alias，或改变参数名

> ~~看来sql应该是静态类型语言~~
>
> 好像又不是

### 默认参数(if else)

```sql
begin
	if cond then
		[statements];
	else
		[statements];
	end if;
	[statements];
end

--- idiom
where attr = IFNULL(param_attr, attr)
```

### 抛出错误

如果参数不合法，可以抛出错误

```sql
IF payment_amount <=0 THEN
	SIGNAL SQLSTATE '22003'
		SET MESSAGE_TEXT = "invalid payment amount";
END IF;
```

> Sqlstate error

> 建议在业务逻辑代码中处理，尽量少在sp中处理

## OUT参数

Syntax :

```sql
CREATE PROCEDURE out_demo(
	param1 [type],
	OUT param2 [type]
)
BEGIN
	SELECT attr
	INTO param2
	FROM ...
END
```

- 参数前加OUT
- select后加INTO

语义：

1. 将`@param2`变量初始化并加入环境
2. 将`@param2`作为参数调用SP
3. SP对`@param2`进行赋值
4. 返回环境中的`@param2`

## 变量

### 用户/session变量

在整个数据库连接中有效。断开连接后清除。

**语法**

```sql
SET @varName = value;
--- 此后@varName在整个连接中有效
SELECT @varName;
```

- 变量名以@开头

**语义**

- 将varName变量添加到整个连接中

Idiom

- 通常和OUT结合使用

### 局部变量

只在单个存储过程中有效。

```sql
CREATE PROCEDURE local_var_demo()
BEGIN
	DECLARE var1 [type];
	DECLARE var2 [type] DEFAULT [defaul-val];
	
	SELECT a, b
	INTO var1, var2
	FROM ...
	WHERE ...;
	
	SET var2 = var1 / var2;
	
	SELECT var1, var2;
END
```

1. 使用declare来声明变量以及类型
   - 可以使用default来设定默认值

2. 使用select ... into来对变量赋值

3. 使用set来对变量赋值

4. 使用select来返回

# 函数

相比于存储过程，函数只能返回单个值

## 创建

```sql
CREATE FUNCTION [name](
    param [type]
) RETURNS [type]
--- 函数属性
DETERMINISTIC --- 结果不依赖于数据库中数据
READS SQL DATA --- 包含查询
MODIFIES SQL DATA --- 包含修改
BEGIN
		[body]
    return [value];
END;
```

## 删除

```sql
DROP FUNCTION IF EXISTS funcName
```

# Mysql触发器 trigger

一段sql代码，在新增、更新或删除时自动触发

> 一般用于保证数据一致性

## 创建

```sql
CREATE TRIGGER payments_after_insert
	AFTER INSERT ON payments --- tableName
	FOR EACH ROW
BEGIN
	 UPDATE invoices
	 SET payment_total = payment_total + NEW.amount
	 WHERE invoice_id = NEW.invoice_id;
END;
```

- 命名一般使用 “表名_after(或before)\_操作(insert/delete/update)”

- 目前mysql只支持单行级触发器
- 使用NEW.attr获取新增行的某属性，使用OLD获取delete的属性

## 查看

```sql
show triggers like "payments%"
```

## 删除

```sql
drop trigger if exits trigger_name
```

## 使用触发器审计(audit)数据

trigger两大用处：保证数据一致性和审计数据(log)

# 事件EVENTS

一个任务(或一个sql代码块)根据规划执行

```sql
show variables like "event%";
set global event_scheduler = ON;
```

- 事件依赖于这个变量

## 创建

```sql
CREATE EVENT yearly_delete_state_audit_rows
ON SCHEDULE
	--- AT "2022-12-31" ---执行一次
	EVERY 2 DAY STARTS NOW() ENDS "2022-12-31"
	--- 其他间隔 如YEAR MONTH HOUR等
	--- starts ends都是可选的
DO BEGIN
	[task]
END;
```

## 查看/drop/修改alter

```sql
show events like "name%";
---
drop events if exists event_name;
--- 修改 ---
alter event ... --- 替换create
alter event event_name DISABLE; ---暂时关闭event
alter event event_name ENABLE; --- 开启event
```

# 事务 in Mysql

> ACID
>
> - 原子性
> - 一致性
> - 隔离性：多个事务作用于相同数据时，同时只有一个事务能修改/更新
> - 持久性

## 创建事务

```sql
START TRANSACTION;
[statement1];
[statement2];
...
COMMIT;
```

- 有时需要手动回滚事务时，则以ROLLBACK结尾

- mysql中会把没有错误的sql封装到事务中（自动提交）。

## 并发和锁

**MYSQL默认行为**

当两个事务同时试图**修改数据**，一个事务会获得控制权，并在事务结束前(COMMIT或rollback)将该行数据**上锁**。

## 并发问题

### LOST UPDATES

- 发生于没有使用锁的情况下，对相同数据在同一时间进行更新
- 后提交的事务覆盖了先提交的事务的执行结果
- 解决方法：使用锁

### 脏读

![image-20220111005635555](https://cdn.jsdelivr.net/gh/Usigned/pic-typora@main/images/image-20220111005635555.png)

- 事务A对数据修改后，在其提交前修改后的数据就被另一事务(B)读取
- 如果事务A回滚了，则事务B读取的数据就是错的
- 解决方法：使用合适的隔离等级
  - mysql中有4个等级，其中read committed让事务只能读取已提交的数据

### NON-REPEATING READ

- 一个事务中多次读相同的数据，由于外部修改了数据，其会返回不同的结果
- 解决方法：在进入事务时**对数据进行快照**，事务期间使用快照。

### Phantom Read

- 一个事务在查询时，其数据由于其他事务修改，导致漏掉了符合条件的数据
- 解决方法：事务期间，**防止外界对数据修改/查询**

## Mysql隔离等级

![image-20220111010749499](../../Library/Application Support/typora-user-images/image-20220111010749499.png)

- mysql默认等级：repeatable read

### 调整等级

```sql
--- 当前等级
show variables like "%isolation%"
--- 给下一个事务设置隔离等级
set transaction isolation level serializable;
--- 给当前连接设置隔离等级
set session transaction isolation level xxx;
--- 设置全局隔离等级，全部顺序执行
set global transaction isolation level xxx;
```

## 死锁

> mysql会自动检测死锁，并rollback事务。

```sql
--- trans1
start transaction;
update table1 ...; --- |<--获取table1的锁
update table2 ...; --- |<--等待table2的锁
commit;
--- trans2
start transaction;
update table2 ...; --- |<--获取table2的锁
update table1 ...; --- |<--等待table1的锁
commit;
```

# MySQL 数据类型

## 字符串

| 类型       | 长度                        |
| ---------- | --------------------------- |
| char(x)    | 定长                        |
| varchar(x) | 变长，最长65,535字符(~64KB) |
| MEDIMTEXT  | 变长，最多16MB              |
| LONGTEXT   | 变长，最多4GB               |
| TINYTEXT   | 变长，255B                  |
| TEXT       | 变长，64KB                  |

- 最好使用varchar，因为可以被索引

## 整形

![image-20220111014322911](https://cdn.jsdelivr.net/gh/Usigned/pic-typora@main/images/image-20220111014322911.png)

- 当试图加入超过范围的数时会报错
- 补零 
  - INT(4) => 0001
  - 只影响显示，不影响存储

## 小数

![image-20220111014706463](https://cdn.jsdelivr.net/gh/Usigned/pic-typora@main/images/image-20220111014706463.png)

- decimal--定点数，计算无精度损失
  - 用于金额
- float/double--浮点数，近似值，范围大，有精度损失
  - 用于科学计算

## 布尔

类型：bool/boolean

值：true(1)/false(0)

## 枚举/集合类型

```sql
ENUM("small", "medium", "big")
SET(...)
```

- 避免使用，不易于扩展
- 可以新建表(look-up table)来存

## 时间

- date
- time
- datetime        8b
- timestamp    4b(up to 2038)
- year

## BLOBS（二进制）

用于存二进制数据

![image-20220111015546616](https://cdn.jsdelivr.net/gh/Usigned/pic-typora@main/images/image-20220111015546616.png)

**最好不要在数据库存二进制数据**

![image-20220111015629260](https://cdn.jsdelivr.net/gh/Usigned/pic-typora@main/images/image-20220111015629260.png)

## JSON对象

类型: JSON

### 创建

```sql
update products
set propeties = JSON_OBJECT(
	'weigth', 10,
	'dimensions', JSON_ARRAY(1,2,3),
	'manufacturer', JSON_OBJECT('name', 'sony')
)
where ...
```

- `JSON_OBJECT(k1,v1,k2,v2,...)`

### 提取

```sql
SELECT JSON_EXTRACT(properties, '$.weight')
from ...

--- alter 1
SELECT properties -> '$.dimensions[0]' from ...

--- alter 2 会去除字符串中引号
select properties ->> '$.manufacturer.name'
```

### 更新

```sql
update products
set properties = JSON_SET(
	properties,
	'$.weight', 20,
	'$.age', 10
)
where ...
```

- `JSON_SET(JSON_OBJ, k1, v1, k2, v2, ...)`
  - 改变属性值，或添加属性

- `JSON_REMOVE(JSON_OBJ, k1, k2...)`
  - 删除属性
- 二者都会返回一个**新的JSON对象**

# 存储引擎

```sql
show engines;

alter table xxx
	engine = InnoDB;
```

常用

- MyISAM
  - 5.5之前，不支持事务、外键等
- InnoDB
  - 支持事务、外键

# 索引indexes

用于加快查询

- 创建索引表（二叉树）
- 索引表一般足够小可以存在内存中

代价

- 数据库更大
- 在更新数据时需要更新索引，影响性能

> Deisgn indexes based on your queries, not your tables

## Explain分析sql执行过程

```sql
explain select * from tableName use index (indexName);
```

- type
  - all，全表扫描
  - ref，非全表
  - Index：全index扫描
- rows：扫描的记录数
- Possible_keys：可选的索引
- Key：执行使用的索引

## 创建索引

```sql
create index idx_columnName on tableName (columnName)

--- create index idx_state on customers (state)
```

## 查看索引

```sql
show indexes in tableName;
```

- 主键自动创建索引

- collation: 排序，A=升序，D=降序
- cardinality：索引中唯一值的估算数量
  - 先执行`analyze table tableName`，再查看索引以精确统计
- Index_type
  - Btree：普通B树索引
  - Fulltext：全文索引

> 聚类索引
>
> - 主键
> - 每张表最多一个
>
> 二级索引
>
> - 创建时mysql会自动添加主键
> - 创建外键时会自动创建对应列的索引

## 前缀索引

存储字符串索引需要大量空间，故将整行做为索引是不效率的

```sql
create index idx_name on customers (last_name(20))
```

- 使用前二十个做索引

**如何决定取多少个做索引？**

索引目的是尽可能多的区分数据，比如一共1000条数据，我们想让索引中唯一值尽可能毕竟这个值

```sql
select
	count(distinct left(last_name, 1)),
	count(distinc left(last_name, 5)),
	count(distinc left(last_name, 10)),
from customers;
```

- 分别查看以1，5，10个字符做标识的唯一值数量
- 选取合适的，数量多，字符数少

## 复合索引

一张表中有多个索引时，mysql也只会选择一个索引，其余的正常查询

此时可以选择多个列做一个索引

```sql
create index idx_state_points on customers (state, points);
```

> 实际上，复合索引比单列索引更好

- 复合索引中列的顺序很重要，应该将常用的、区分度高(cardinality)的放在前面
  - 需要根据查询来决定

## 使用索引优化or查询

1. 拆分为union查询
2. 对每一段查询建立合适的索引

## 全文索引

用于加速某行字符串匹配，比如将博客的标题、内容存在数据库的列中，用户使用关键字查询

```sql
select *
from post
where title like '%react redux%' or 
	body like "%react redux%";
```

- 普通方法使用like匹配很长字符串是低效的

**创建全文索引**

```sql
create fulltext index idx_title_body on posts (title, body);
```

**使用全文索引**

```sql
select *, 	match(title, body) against("react redux")
from posts
where 
	match(title, body) --- 必须包含所有索引的列
  against("react redux");
```

- `match(title, body) against("react redux")`会返回一个相关性的评分(0-1间)

**boolean mode**

强制包含或排除指定字符串

- in boolean mode
- -str1：排除st1
- +str2：包含str2
- `"handling a form"`：包含短语

```sql
...
match(title, body) against('react -redux +form' in boolean mode);
```

- 搜索和`react`相关、不包含`redux`，包含`form`的内容

## 查看查询代价

```sql
show status like "last_query_cost";
```

- 会计算排序代价
- 使用索引优化order by
  - 复合索引中，数据会先按第一个索引排序，然后在排序结果中按第二索引排序
- Select * 会影响性能，因为索引不能cover所有列
  - 故应该给经常查询的列加索引，然后只select哪些列

# 权限管理

## 创建用户

```sql
create user john --- 任何ip
	--- join@127.0.0.1 ip
	--- john@localhost 主机名
	--- john@abc.com 域名
	--- john@'%.abc.com' 子域名
	identified by "1234";
```

- %通配符用法和字符串中的基本相同

## 查看用户

```sql
--- 方法1
select * from mysql.user;
```

## 删除用户

```sql
drop user john@localhost;
```

## 修改密码

```sql
--- 当前用户
set password = '1234';
--- 其他用户
set password john = '1234';
```

## 授予权限

1. 读写权限，不能改变表 

```sql
--- 典型的用户权限
grant select, insert, update, delete, execute
on sql_store.* --- 选择表
to john@localhost;
```

2. Admin

```sql
--- 典型管理员权限
grant all ---所有权限
on *.* --- 所有数据库中所有表
to admin;
```

> 权限列表[MySQL :: MySQL 8.0 Reference Manual :: 6.2.2 Privileges Provided by MySQL](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html)

## 查看权限

```sql
show grants on john;
```

## 撤销权限

```sql
revoke [privilegs]
on [db].[table]
from userName
```



# 一些关于SQL的思考

其实数据库书中有区分查询语言SQL和管理语言SML

- 现在看来SQL的执行结果可以作为SML的输入
- SQL的具体执行结果到底如何，目前尚未看出
- 单列的SQL查询结果应该等同于元组(1, 2, 3)，可以被用于IN

- 使用()打包单个值，使其可以被IN/USING/Value等处理

> select/from/where都可以使用子查询的结果

> 感觉mysql中sql的语义还挺复杂的，一点都不会比普通的编程语言简单

---

## SQL语句结构理解

```sql
----------------------
------stage-1---------
----------------------
select 
----------------------
------stage-2---------
----------------------
from
where
----------------------
------stage-3---------
----------------------
group by
having
order by
```

个人理解sql语句分为三部分

1. `select`用于定义整个查询的元信息
2. `from/where`从磁盘io数据到内存（查询）
3. `group by/having/order by`以及`select`中的函数负责查询结果后处理，包括**计算**、**分组**、**重命名**

Reasons:

1. `where`中无法使用`select`中对列的alias
   - 因为`select`中的alias是在数据查询完成后（第二步）对内存中数据做后处理，而`where`执行时数据还没从磁盘中io出来
2. `having`不会影响整体数据查询的磁盘io量（可通过`explain`验证），且可以使用`select`中的alias
   - 因为`having`是在第二步完成后对数据做后处理，因此对查询数据量无影响。同时其可以使用`select`的alias，说明`select`对数据重命名发生在查询完成后
3. `select`中的字段数会影响查询效率（一直说要避免使用`select *`），因此`select`中的元数据即影响第二步和第三步，而不是仅影响第三步（若如此，无论`select`什么字段第二步都查表中所有字段并根据`select`的参数在第三步后处理时才筛选，该种实现方式简洁但效率低下）。