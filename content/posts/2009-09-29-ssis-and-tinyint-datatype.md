---
title: SSIS and Tinyint datatype
author: alan
type: post
date: 2009-09-29T12:47:40+00:00
url: /ssis-and-tinyint-datatype/
categories:
  - SQL Server
tags:
  - datatypes
  - ssis

---
I've had a couple of issues recently with using the tinyint datatype in SSIS. In one case I have a Lookup Transform which joins to the incoming data flow path on a column defined as a tinyint in the database. The column metadata in the data flow is DT_I4, a four-byte signed integer. Dragging this across to the tinyint column in the Lookup gives me the following error:  

```
The following columns cannot be mapped:
[DataFlowColumn, LookupColumn]
One or more columns do not have supported data types, or their data types do not match.
```

By process of elimination, I discovered the incoming column needs to be converted to a DT_UI1, a single-byte unsigned integer. One to bear in mind for the future!