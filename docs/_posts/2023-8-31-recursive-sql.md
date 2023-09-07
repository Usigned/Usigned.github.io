---
title: sql递归查询
tags: [sql]
---

使用with创建虚拟表实现sql递归查找树形结构的数据，需要数据库支持递归查找

```sql
-------- part 1 ----------
with recursive tmp as (
	select id, parent_id from your_table where id = 1
  union all
-------- part 2 ----------
  select tmp.id, tmp.parent_id
  from tmp
  join your_table on tmp.id = your_table.parent_id
)
-------- part 3 ----------
select *
from tmp;
```