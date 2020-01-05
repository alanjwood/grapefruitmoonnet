---
title: Formatting Dates in SSRS
author: alan
type: post
date: 2009-01-07T12:19:00+00:00
url: /formatting-dates-in-ssrs/
posterous_02249d33a2405d1eae69ce626eb5a028_post_id:
  - 322085
posterous_02249d33a2405d1eae69ce626eb5a028_permalink:
  - http://sqlserver.posterous.com/formatting-dates-in-ssrs
categories:
  - SQL Server
tags:
  - Dates
  - Formatting
  - Reporting Services
  - SSRS

---
In Reporting Services, formatting dates can become a headache for the developer, often due to differing regional settings on servers, development boxes etc.  Therefore I've found it much more useful to force the report to use a particular format, rather than allow the server settings to decide for me.  Previously I would have used FormatDateTime in the expression, as shown here:

`=FormatDateTime(Fields!DateStamp.Value, DateFormat.ShortDate)`

Depending on the regional settings, this could return either 12/3/2008 or 3/12/2008.  When designing the report, I want to know exactly what the date will look like, no matter where it is deployed.  So I instead use the Format function to allow me to choose the format I require.  Some examples:

`=Format(Fields!DateStamp.Value, "dd-MMM-yy")`
  
_returns 03-Dec-08_

`=Format(Fields!DateStamp.Value, "dd-MMM-yyyy")`
  
_returns 03-Dec-2008_

`=Format(Fields!DateStamp.Value, "dd-MMMM-yy")`
  
_returns 03-December-08_

`=Format(Fields!DateStamp.Value, "dd-MMM-yyyy H:mm")`
  
_returns 03-Dec-2008 16:44_

This method removes a lot of the issues regarding date formatting, and means there are no shocks or questions when it comes to deployment time!

_Edit:_ Setting the Language property of the report to your locale will also ensure that dates in your report parameters will have the correct format.  This is something that is often overlooked.

![graphdates][graphdates]

[graphdates]: /img/wp-content/uploads/2009/01/graphdates1.jpeg