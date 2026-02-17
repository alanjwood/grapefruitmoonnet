---
title: Table variables, identity columns and reseeding
author: alan
type: post
date: 2012-07-25T17:00:11+00:00
url: /table-variables-identity-columns-and-reseeding/
categories:
  - SQL Server
tags:
  - identity columns
  - reseed
  - scope
  - t-sql
  - variables

---
Ever tried to reseed an identity column in a table variable? No? Let's give it a go…

Let's declare a simple table variable, @country, and insert a few rows.

```sql
DECLARE @country TABLE
(
 CountryId INT IDENTITY(1,1),
 CountryName VARCHAR(50) NOT null
);

INSERT INTO @country
 ( CountryName )
VALUES
 ( 'Scotland' ),
 ( 'England' ),
 ( 'Wales' ),
 ( 'Northern Ireland' ) ;

SELECT
 CountryId ,
 CountryName
FROM @country ;
```

Truncating a normal table will remove all the rows and reseed the identity value. The following syntax is not valid for a table variable however:

```sql
TRUNCATE TABLE @country;
```

The following error would be returned: _Incorrect syntax near &#8216;@country'._

We can remove all the rows from the table by using DELETE rather than truncate, but this does not reset the identity value. Can we use DBCC CHECKIDENT, like we would on a physical table? No…

```sql
DBCC CHECKIDENT (@country, RESEED, 1);
```

will return _Must declare the scalar variable &#8220;@country&#8221;._, and

```sql
DBCC CHECKIDENT ([@country], RESEED, 1);
```

will return _Cannot find a table or object with the name &#8220;@country&#8221;. Check the system catalog._

Table variables cannot be dropped, nor can they be re-declared. In fact, trying to do the latter returns this error: _&#8220;The variable name &#8216;@country' has already been declared. **Variable names must be unique within a query batch or stored procedure.**&#8220;_ The bold part of this message is important to note. Bear this in mind while we try to declare the table variable in it's own scope:

```sql
DECLARE @i INT = 1,
 @count INT = 5;

WHILE @i <= @count
BEGIN

-- declare table variable in scope of the while loop
 DECLARE @country TABLE
 (
 CountryId INT IDENTITY(1,1),
 CountryName VARCHAR(50) NOT null
 );

INSERT INTO @country
 ( CountryName )
 VALUES
 ( 'Scotland' );

SELECT
 CountryId ,
 CountryName
 FROM @country ;

DELETE FROM @country ;

 SET @i = @i + 1 ;
END

```

The output is as follows:

![reseedinscope][reseed]

[reseed]: /img/wp-content/uploads/2012/07/reseedinscope.png

Note that even though we declared the table every time the loop was executed, no error was returned (even though it was being declared in the same batch) and the identity column continued to increment.

So really, this is just an extremely long-winded way of saying...if you need to reseed an identity column and you are using a table variable, consider using a temporary table or a physical table instead!


[1]: http://www.grapefruitmoon.net/wp-content/uploads/2012/07/reseedinscope.png