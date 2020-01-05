---
title: T-SQL to drop all Foreign Keys that reference a table
author: alan
type: post
date: 2012-06-11T16:45:45+00:00
url: /t-sql-to-drop-all-foreign-keys-that-reference-a-table/
categories:
  - SQL Server
tags:
  - foreign keys
  - scripts
  - tsql

---
In a recent data migration project, I had a task to re-number some primary key columns for certain tables, so that there was no key overlap when a number of systems were combined. Part of this process involved identifying and dropping all the foreign keys that referenced the primary key on the tables that were being changed, then adding them again after the updates were completed. To save a bit of time, I knocked up this little script to generate the DROP and ALTER statements for the referencing foreign keys for a given table.

```sql
SELECT
    'ALTER TABLE ' + s.name + '.' + OBJECT_NAME(fk.parent_object_id)
		+ ' DROP CONSTRAINT ' + fk.NAME + ' ;' AS DropStatement,
    'ALTER TABLE ' + s.name + '.' + OBJECT_NAME(fk.parent_object_id)
	+ ' ADD CONSTRAINT ' + fk.NAME + ' FOREIGN KEY (' + COL_NAME(fk.parent_object_id, fkc.parent_column_id) 
		+ ') REFERENCES ' + ss.name + '.' + OBJECT_NAME(fk.referenced_object_id) 
		+ '(' + COL_NAME(fk.referenced_object_id, fkc.referenced_column_id) + ');' AS CreateStatement
FROM
	sys.foreign_keys fk
INNER JOIN sys.foreign_key_columns fkc ON fk.object_id = fkc.constraint_object_id
INNER JOIN sys.schemas s ON fk.schema_id = s.schema_id
INNER JOIN sys.tables t ON fkc.referenced_object_id = t.object_id
INNER JOIN sys.schemas ss ON t.schema_id = ss.schema_id
WHERE
	OBJECT_NAME(fk.referenced_object_id) = 'account'
	AND ss.name = 'dbo';
```