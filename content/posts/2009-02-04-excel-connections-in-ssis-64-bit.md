---
title: Excel connections in SSIS 64-bit
author: alan
type: post
date: 2009-02-04T14:29:02+00:00
url: /excel-connections-in-ssis-64-bit/
posterous_02249d33a2405d1eae69ce626eb5a028_post_id:
  - 388965
posterous_02249d33a2405d1eae69ce626eb5a028_permalink:
  - http://sqlserver.posterous.com/excel-connections-in-ssis-64-b
categories:
  - SQL Server
tags:
  - 64-bit
  - excel
  - ssis

---
This is a new one on me...when running SSIS in a 64-bit environment, Excel connections (and Access as well I presume) do not work, as the OLE DB Provider for Jet is not supported.

At a current client, they are moving the server platform from Windows 2003 Server to Windows 2008 Server 64-bit. I had developed some ETL packages for them, pulling data from Excel and CSV sources. The packages get executed when the files have been unzipped and downloaded to a specified folder. This is done via simple batch files, which are along the lines of the following:

```dtexec /f "F:SSIS PackagesReportingImportClientCSV.dtsx"```

When I moved all the packages to Windows 2008, the CSV packages ran no problem. But when I tried to execute one that imported from Excel, I received the following error:

```Code: 0xC00F9304```

``` Source: ImportClientXLS Connection manager "Excel Connection Manager"```
  
``` Description: SSIS Error Code DTS_E_OLEDB_EXCEL_NOT_SUPPORTED: The Excel Connection Manager is not supported in the 64-bit version of SSIS, as no OLE DB provider is available.```

In order to use the OLE DB for Jet providers, the packages must be run in 32-bit mode. This is done by using the x86 version of dtexec and specifying the "/X86" switch. I amended the batch file in question to look similar to this:

```"C:Program Files (x86)Microsoft SQL Server100DTSBinndtexec.exe" /f "F:SSIS PackagesReportingImportClientXLS.dtsx" /X86```

Voila! The package ran successfully.

This link from Microsoft details the limitations when running SSIS in 64-bit environments: [64-bit Considerations for Integration Services][1]

 [1]: http://msdn.microsoft.com/en-us/library/ms141766.aspx