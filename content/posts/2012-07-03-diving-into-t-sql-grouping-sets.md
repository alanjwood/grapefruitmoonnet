---
title: Diving into T-SQL Grouping Sets
author: alan
type: post
date: 2012-07-03T13:21:39+00:00
url: /diving-into-t-sql-grouping-sets/
categories:
  - SQL Server
tags:
  - aggregates
  - group by
  - t-sql

---
The Grouping Sets feature was added to the T-SQL GROUP BY clause with the release of SQL Server 2008. Despite its usefulness, I've not come across many (or any) of them in the wild at any of the clients I've worked at in the past few years. Here is a primer on how to use them, using the AdventureWorks OLTP database.

### SQL Server 2005 Style

In SQL Server 2005, we could use the WITH ROLLUP syntax to add an aggregated total row:

```sql
SELECT
    st.Name ,
    SUM(soh.TotalDue) AS TotalDue
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom')
GROUP BY
	st.Name WITH ROLLUP;
```

![withrollup][withrollup]

[withrollup]: /img/wp-content/uploads/2012/07/1.png

The WITH ROLLUP and WITH CUBE features will be removed in a future version of SQL Server.

### Basic Grouping Sets

With SQL Server 2008 onwards, we can use the GROUPING SETS syntax to achieve the same results. The grouping set is a defined grouping of data in a query. Each grouping set is defined by placing the columns in parentheses. An empty group, (), will show an aggregation across all sets. Here we define a grouping set for territory name, and an overall aggregation:

```sql
SELECT
    st.Name ,
    SUM(soh.TotalDue) AS TotalDue
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom')
GROUP BY
	GROUPING SETS ((st.Name), ());
```

![Basic Grouping Set][Basic Grouping Set]

[Basic Grouping Set]: /img/wp-content/uploads/2012/07/2.png

Another way of expressing this is to use GROUP BY ROLLUP():

```sql
SELECT
    st.Name ,
    SUM(soh.TotalDue) AS TotalDue
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom')
GROUP BY
	ROLLUP (st.Name);
```

![GROUP BY ROLLUP][GROUP BY ROLLUP]

[GROUP BY ROLLUP]: /img/wp-content/uploads/2012/07/3.png

### Adding another column into the Grouping Set

Grouping Sets obviously become more useful when dealing with multiple columns for grouping. In this next query, we'll add in a column for the order year, and define two grouping sets; one for the territory name and one for the year.

```sql
SELECT
    st.Name ,
    YEAR(soh.OrderDate) AS Yr ,
    SUM(soh.TotalDue) AS TotalDue
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom') AND soh.OrderDate &gt;= '20070101'
GROUP BY
	GROUPING SETS ((st.Name), (YEAR(soh.OrderDate))) ;
```

![Grouping sets with two columns][Grouping sets with two columns]

[Grouping sets with two columns]: /img/wp-content/uploads/2012/07/4.png

Let's expand this out and amend the second set to include the territory name. This will return aggregates rows for both the name and year and the name alone.

```sql
SELECT
    st.Name ,
    YEAR(soh.OrderDate) AS Yr ,
    SUM(soh.TotalDue) AS TotalDue
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom') AND soh.OrderDate &gt;= '20070101'
GROUP BY
	GROUPING SETS ((st.Name), (st.Name, YEAR(soh.OrderDate))) ;
```

![Grouping Sets with two columns in one set][Grouping Sets with two columns in one set]

[Grouping Sets with two columns in one set]: /img/wp-content/uploads/2012/07/5.png

The overall aggregate can be returned by using the empty grouping set.

```sql
SELECT
    st.Name ,
    YEAR(soh.OrderDate) AS Yr ,
    SUM(soh.TotalDue) AS TotalDue
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom') AND soh.OrderDate &gt;= '20070101'
GROUP BY
	GROUPING SETS ((st.Name), (st.Name, YEAR(soh.OrderDate)), ()) ;
```

![Grouping sets with overall total][Grouping sets with overall total]

[Grouping sets with overall total]: /img/wp-content/uploads/2012/07/6.png

To group on everything, all columns and combinations can be defines in their own grouping sets:

```sql
SELECT
    st.Name ,
    YEAR(soh.OrderDate) AS Yr ,
    SUM(soh.TotalDue) AS TotalDue
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom') AND soh.OrderDate &gt;= '20070101'
GROUP BY
	GROUPING SETS ((st.Name), (YEAR(soh.OrderDate)), (st.Name, YEAR(soh.OrderDate)), ()) ;
```

![Using grouping sets to group on everything][Using grouping sets to group on everything]

[Using grouping sets to group on everything]: /img/wp-content/uploads/2012/07/7.png

### Using the GROUPING function

We can identify whether a column has been aggregated by the use of grouping sets (or ROLLUP and CUBE) by using the GROUPING function. It will return 1 for aggregated or 0 for not aggregated. The following query uses this to identify if the territory name column has been aggregated:

```sql
SELECT
    st.Name ,
    YEAR(soh.OrderDate) AS Yr ,
    SUM(soh.TotalDue) AS TotalDue,
    GROUPING(st.Name) AS TerritoryGrouped
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom') AND soh.OrderDate &gt;= '20070101'
GROUP BY
	GROUPING SETS ((st.Name), (YEAR(soh.OrderDate)), (st.Name, YEAR(soh.OrderDate)), ()) ;
```

![Using the GROUPING function][Using the GROUPING function]

[Using the GROUPING function]: /img/wp-content/uploads/2012/07/8.png

### Using the GROUPING_ID function

The GROUPING_ID function in T-SQL computes the level of grouping. The grouping columns can be passed in to return an INT which will show the level. For example:

```sql
SELECT
    st.Name ,
    YEAR(soh.OrderDate) AS Yr ,
    SUM(soh.TotalDue) AS TotalDue,
    GROUPING(st.Name) AS TerritoryGrouped,
    GROUPING_ID(st.Name, YEAR(soh.OrderDate)) AS GrpLevel
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom') AND soh.OrderDate &gt;= '20070101'
GROUP BY
	GROUPING SETS ((st.Name), (YEAR(soh.OrderDate)), (st.Name, YEAR(soh.OrderDate)), ()) ;
```

![Using the GROUPING_ID function][Using the GROUPING_ID function]

[Using the GROUPING_ID function]: /img/wp-content/uploads/2012/07/9.png

This can be used to filter rows on certain levels by using the HAVING clause:

```sql
SELECT
    st.Name ,
    YEAR(soh.OrderDate) AS Yr ,
    SUM(soh.TotalDue) AS TotalDue,
    GROUPING(st.Name) AS TerritoryGrouped,
    GROUPING_ID(st.Name, YEAR(soh.OrderDate)) AS GrpLevel
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom') AND soh.OrderDate &gt;= '20070101'
GROUP BY
	GROUPING SETS ((st.Name), (YEAR(soh.OrderDate)), (st.Name, YEAR(soh.OrderDate)), ()) 
HAVING
	GROUPING_ID(st.Name, YEAR(soh.OrderDate)) = 3 ;
```

![Using GROUPING_ID to filter rows][Using GROUPING_ID to filter rows]

[Using GROUPING_ID to filter rows]: /img/wp-content/uploads/2012/07/10.png

Another use of this is for labelling the grouping rows:

```sql
SELECT
    st.Name ,
    YEAR(soh.OrderDate) AS Yr ,
    SUM(soh.TotalDue) AS TotalDue,
    GROUPING(st.Name) AS TerritoryGrouped,
    CASE GROUPING_ID(st.Name, YEAR(soh.OrderDate)) 
		WHEN 0 THEN 'Territory & Year Total'
		WHEN 1 THEN 'Territory Total'
		WHEN 2 THEN 'Year Total'
		WHEN 3 THEN 'Overall Total'
    END	AS GrpLevel
FROM
    Sales.SalesOrderHeader soh
INNER JOIN Sales.SalesTerritory st ON soh.TerritoryID = st.TerritoryID
WHERE st.Name IN ('Germany', 'United Kingdom') AND soh.OrderDate &gt;= '20070101'
GROUP BY
	GROUPING SETS ((st.NAME), (YEAR(soh.OrderDate)), (st.Name, YEAR(soh.OrderDate)), ()) ;
```

![Using GROUPING_ID to label rows][Using GROUPING_ID to label rows]

[Using GROUPING_ID to label rows]: /img/wp-content/uploads/2012/07/11.png

Grouping sets and grouping functions add some powerful functionality to the standard GROUP BY clause. Let's hope I see them being used more in the future. 🙂

References and further reading:
  
[SQL Server Books Online: GROUP BY](http://technet.microsoft.com/en-us/library/ms177673.aspx)
  
[SQL Server Books Online: GROUPING](http://technet.microsoft.com/en-us/library/ms178544.aspx)

[SQL Server Books Online: GROUPING_ID](http://technet.microsoft.com/en-us/library/bb510624.aspx)