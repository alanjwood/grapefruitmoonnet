---
title: 'T-SQL Tuesday: Return a range of dates between 2 days'
author: alan
type: post
date: 2009-12-08T14:26:00+00:00
url: /t-sql-tuesday-return-a-range-of-dates-between-2-days/
categories:
  - SQL Server
tags:
  - Dates
  - tsql

---

![results1][results1]

[results1]: /img/wp-content/uploads/2009/12/results1.png

Just after Adam Machanic announced the first of the T-SQL Tuesday blog events, I was asked a question about how to return a list of all the dates between two given days.  I came across some good forum posts about how to do so, many of which utilised numbers tables.  I wanted to avoid that (nothing against them, just wanted something that was stand-alone), and figured that a CTE would be the best way to go; in fact someone had posted such a method, a little of which I have used here (if I can find the post again I'll give them a mention in the comments!).

The following procedure takes 3 parameters: a StartDate, an EndDate and a bit flag to say if the range should be inclusive (that is, will it also return the StartDate and EndDate in the results), defaulted to 1.  A list of all the dates between the first two parameters will be returned.

The maxrecursion option has been set to 3660, giving a maximum of ten years dates to return.

```sql
create procedure dbo.GetDateRange
@StartDate datetime,@EndDate datetime,@Inclusive bit = 1as

set nocount on;

declare @DayDiff smallint;
set @DayDiff = datediff(dd, @StartDate, @EndDate);
if @Inclusive = 0set @DayDiff = @DayDiff - 1;

with cteRange (DateRange)
as (select dateadd(dd, datediff(dd, 0, @EndDate) - @DayDiff, 0)
  union allselect dateadd(dd, 1, DateRange)
  from cteRange
  where dateadd(dd, 1, DateRange) < (@EndDate + @Inclusive)
  )
select DateRange
from cteRangeoption (maxrecursion 3660);
go
```

Run the procedure using the following code:

```sql
exec dbo.GetDateRange '2009-12-07', '2009-12-12'
```

and the results are as shown in the image at the top of the post.

Hope some of you find this useful.  This post is part of <a href="http://sqlblog.com/blogs/adam_machanic/archive/2009/11/30/invitation-to-participate-in-t-sql-tuesday-001-date-time-tricks.aspx" target="_blank">Adam Machanic's T-SQL Tuesday</a>.