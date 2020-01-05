---
title: SSIS 2008 Deployment Manifest error
author: alan
type: post
date: 2011-02-01T14:50:00+00:00
url: /ssis-2008-deployment-manifest-error/
posterous_02249d33a2405d1eae69ce626eb5a028_post_id:
  - 41637442
posterous_02249d33a2405d1eae69ce626eb5a028_permalink:
  - http://sqlserver.posterous.com/ssis-2008-deployment-manifest-error
categories:
  - SQL Server
tags:
  - ssis

---
I had an issue this morning whereby I was trying to deploy an SSIS 2008 project using the Deployment Manifest, and was hitting the following error:

>  Could not save the package "Package Path" to SQL Server "SQL Server".

>ADDITIONAL INFORMATION:The package failed to load due to error 0xC0011008 "Error loading from XML. No further detailed error information can be specified for this problem because no Events object was passed where detailed error information can be stored.". This occurs when CPackage::LoadFromXML fails.

A quick Google search indicated that the issue was to do with a SQL Server 2005 installation that had been upgraded to 2008.  Right-clicking the Deployment Manifest file showed two options for the Installation Package Utility.  Even though both said 2005, one was actually pointing to the 2008 version.  Choosing this one corrected the problem.  The default location for the 2008 version is

`C:Program FilesMicrosoft SQL Server100DTSBinndtsinstall.exe`

You can also run this via the command line:

`C:Program FilesMicrosoft SQL Server100DTSBinndtsinstall.exe "C:<project path>ProjectName.SSISDeploymentManifest"`
  
![deploymenterror1][deploymenterror1]

[deploymenterror1]: /img/wp-content/uploads/2011/02/deploymenterror1.jpeg

![utilityoptions1][utilityoptions1]

[utilityoptions1]: /img/wp-content/uploads/2011/02/utilityoptions1.jpeg