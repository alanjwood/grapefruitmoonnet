---
title: Case-sensitivity in a T-SQL WHERE clause
author: alan
type: post
date: 2012-06-22T18:00:17+00:00
url: /case-sensitivity-in-a-t-sql-where-clause/
categories:
  - SQL Server
tags:
  - collation
  - tsql

---
I was asked today if it was possible to use a case-sensitive LIKE statement in a WHERE clause. The default SQL Server collation is case-insensitive, but the collation used can be specified in a query. To return the collation of the current database:

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(),'Collation');
```

In my case, this returned Latin1\_General\_CI\_AS. The case-sensitive version of this is Latin1\_General\_CS\_AS. To use this collation for a column, specify it after the column using the COLLATE statement. For example, I have a table called Groups, with 2 rows. The first row has a GroupName of "Admin", and the value in the second row is &#8220;Standard". The following query returns both rows:

```sql
SELECT GroupID ,
    GroupName
FROM dbo.Groups
WHERE GroupName LIKE '%a%'
```

Results:

```
GroupID	GroupName
1	Admin
2	Standard
```

However, when we use the COLLATE statement to use the case-sensitive collation, only the second row is returned:

```sql
SELECT GroupID ,
    GroupName
FROM dbo.Groups
WHERE GroupName COLLATE Latin1_General_CS_AS LIKE '%a%'
```

Results:

```
GroupID	GroupName
2	Standard
```

Practical uses for this could include exact string searches and password matching.