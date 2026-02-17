---
title: Identify the T-SQL being run by a SPID
author: alan
type: post
date: 2009-10-02T14:12:33+00:00
url: /identify-the-t-sql-being-run-by-a-spid/
categories:
  - SQL Server
tags:
  - tsql

---
This is one I used to use a lot, and had cause to use it this morning. An ETL process to truncate a staging table was being blocked by another process on the server, and I needed to identify exactly what command the blocking process was attempting to execute. I found the SPID by executing sp_who2, which showed me the SPID that was blocking the truncate command (in this case SPID 54). I then executed the following command to find out exactly what command was causing the block to happen:

```sql
DBCC inputbuffer(54)
```

The result set returned shows the actual T-SQL that was last executed for that SPID, which was a very large insert from a remote PostgreSQL database. 

This is a quick and useful method for tracking down issues and investigating what is happening on your server.