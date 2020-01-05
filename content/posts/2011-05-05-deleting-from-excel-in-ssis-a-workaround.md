---
title: Deleting from Excel in SSIS – a workaround
author: alan
type: post
date: 2011-05-05T07:03:00+00:00
url: /deleting-from-excel-in-ssis-a-workaround/
posterous_02249d33a2405d1eae69ce626eb5a028_post_id:
  - 51889844
posterous_02249d33a2405d1eae69ce626eb5a028_permalink:
  - http://sqlserver.posterous.com/deleting-from-excel-in-ssis-a-workaround
categories:
  - SQL Server
tags:
  - excel
  - ssis

---
Here's a quick how-to post, a little work-around for an issue with writing to Excel from SSIS.

I've been asked a few times how to use SSIS to delete data from an Excel spreadsheet, then write data into that blank spreadsheet, while maintaining the column headers in the first row.  There is no particularly straightforward way of doing so in SSIS, as attempting a delete statement on the Excel sheet will either remove the data in all rows (including the header) or throw an error as the DELETE statement is not supported.  There is a method that I use, which I will step through here.

The basic idea is to do the following on each execution of the package:

1. Delete the XLS file that is being written to

2. Re-create the XLS using a CREATE TABLE statement

3. Load the data into the empty XLS

In order to do this, start by creating the Data Flow for step 3 and ensure that this works as expected (Figure 1 is a simple example).  This will create the XLS that will be written to.  Now we have the structure, we can use this to re-create the XLS using a SQL statement.  To do so, drop an Excel Source onto the flow (pointing to the destination file) and then an Excel Destination, and link the output from source to destination.  Open the Excel Destination and click the New button next to &#8216;Name of the Excel sheet' &#8211; this will pop up a window with the CREATE TABLE statement that will be used in step 2.  Copy this and delete both the Excel Source and Destinations as they have served their purpose for this exercise.

![Figure1][figure1]

[figure1]: /img/wp-content/uploads/2011/05/figure1-scaled50011-194x300.png

Figure 1

On the Control Flow, add an Execute SQL Task, with the ConnectionType set to Excel, and the Connection set to the destination Excel connection manager.  Paste in the the CREATE TABLE script from the previous step into the SQL Statement property value.  This will create the XLS at run time.

Next, add a File System Task, with an Operation of Delete File and the Source Connection set to a new File Connection Manager, also pointing to the destination XLS.

Finally, set up the Control Flow to execute the delete, then execute the CREATE TABLE, then perform the Data Flow, as shown in Figure 2.

![Figure2][figure2]

[figure2]: /img/wp-content/uploads/2011/05/figure2-scaled5002.png

Figure 2

One issue I found with this method: the Execute SQL Task connected to an Excel Connection Manager did not work correctly if the Excel version was Microsoft Excel 2007 (.xlsx extension).  Changing this to Excel 97-2003 (.xls) resolved this issue.