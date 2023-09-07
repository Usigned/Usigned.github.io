---
title: sql中的with关键字和窗口函数
tags: [sql]
---

在oltp系统里，通常sql都比较简答(crud)，而在olap场景中由于是面向分析的故sql都普遍更长更复杂，此时`with`关键字和窗口函数就经常被使用到。

## with

### 作用

- 建立一个仅在当前sql语句中生效的临时表，该临时表的结果可以在当前sql中复用

#### 语法

```sql
with 
	alias1 as (
    ---子查询1---
    select *
    from your_table
  ), 
	alias2 as (
  	---子查询2---
    select *
    from alias1
  )
select alias1.*, alias2.*
from alias1
join alias2 on alias1.id = alias2.id
```

- `with`申明子查询（临时表）后必须接`select`
- 申明创建后的临时表可以被后续临时表使用
  - 如alias1表可以被alias2使用

- 使用`with`创建临时表和在`from`中使用子查询的区别是
  - `with`创建的临时表在顶层，可以在整句sql中使用
  - [ ] 性能是否存在区别？

### 递归

使用`with`创建临时表并和自己关联以实现对树形结构数据的搜索，详见[sql递归](./2023-8-31-recursive-sql.md)

## 窗口函数

### 作用

类似聚合函数+group by，但不会减少数据条数而是对每条数据都执行操作

### 语法

```sql
func() over (partition by col1, col2 order by col3)
```

详见[sql窗口函数](2023-7-18-sql-in-mysql.md#窗口函数)

