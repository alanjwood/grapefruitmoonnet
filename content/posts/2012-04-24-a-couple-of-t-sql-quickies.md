---
title: A Couple Of T-SQL Quickies
author: alan
type: post
date: 2012-04-24T19:18:44+00:00
url: /a-couple-of-t-sql-quickies/
categories:
  - SQL Server
tags:
  - t-sql

---
It's been a while since I've posted any SQL Server content, so in a bid to get back into the swing of things, here are a couple of very simple, but useful, T-SQL commands. I've used these recently in a large project I'm working on.

**Identify all tables with no Foreign Key constraints:**

```sql
select distinct<br />
        t.name<br />
from    sys.tables t<br />
        left outer join sys.foreign_keys fk on t.object_id = fk.parent_object_id<br />
where   fk.object_id is null<br />
order by t.name;
```

**List all tables in a database, with row counts**

```sql
select  t.name ,<br />
        ps.row_count<br />
from    sys.dm_db_partition_stats ps<br />
        inner join sys.tables t on ps.object_id = t.object_id<br />
where   ps.index_id < 2
order by t.name;
```