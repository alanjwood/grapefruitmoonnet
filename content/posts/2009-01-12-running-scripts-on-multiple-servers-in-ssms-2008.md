---
title: Running scripts on multiple servers in SSMS 2008
author: alan
type: post
date: 2009-01-12T13:59:00+00:00
url: /running-scripts-on-multiple-servers-in-ssms-2008/
categories:
  - SQL Server
tags:
  - SSMS

---
SQL Server Management Studio 2008 has a new feature, the ability to run a script on multiple servers at once.  To set this up, the servers need to be registered to a Server Group in the Registered Servers window (see image).  Right-clicking on the Server Group and selecting New Query creates a query window which is connected to the servers in the group (as long as the connection information is correct and the passwords, if using SQL Server Authentication, are saved).  Running a simple query on the master database, in this case:

```sql
select top 5 * from sys.columns
```

returns the top 5 rows from that table from each server.  See the results in the attached image.

The option to Merge Results is True by default.  In a case where the schemas are not identical, this would need to be set to False otherwise an error will be thrown.

![1regservers1][1regservers1]

[1regservers1]: /img/wp-content/uploads/2009/01/1regservers1.jpeg

![2options1][2options1]

[2options1]: /img/wp-content/uploads/2009/01/2options1.jpeg

![3queryresults1][3queryresults1]

[3queryresults1]: /img/wp-content/uploads/2009/01/3queryresults1.jpeg