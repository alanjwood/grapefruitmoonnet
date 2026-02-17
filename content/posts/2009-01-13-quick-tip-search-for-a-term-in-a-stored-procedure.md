---
title: 'Quick tip: Search for a term in a Stored Procedure'
author: alan
type: post
date: 2009-01-13T15:25:00+00:00
url: /quick-tip-search-for-a-term-in-a-stored-procedure/
categories:
  - SQL Server
tags:
  - catalogs
  - stored procs
  - tips
  - tsql

---
Need to find out if a term is referenced in any stored procedures in your database?&nbsp; Use the following T-SQL statement...

```sql
select name, definition   
from sys.procedures p  
inner join sys.sql_modules m   
on p.object_id = m.object_id  
where m.definition like '%search term%';
```

This will work in SQL Server 2005 and 2008.