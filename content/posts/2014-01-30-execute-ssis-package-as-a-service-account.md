---
title: Execute SSIS package as a Service Account
author: alan
type: post
date: 2014-01-30T11:58:22+00:00
url: /execute-ssis-package-as-a-service-account/
categories:
  - SQL Server
tags:
  - credentials
  - proxies
  - security
  - sql agent
  - ssis

---
At my current client, I have a very specific set of parameters to work within regarding security. Chiefly, I need read permissions on the underlying database for a Microsoft Dynamics AX 2012 R2 installation. I have a series of SSIS packages that extract data from AX on a daily basis, executed from SQL Server Agent. The AX database, however, is hosted on a remote server and neither myself or the client team have permissions directly on this box. Our only option for connecting to the SQL Server is via a service account which has db_datareader permissions on the AX database. So how to execute the Agent jobs running as the service account? By using a Credential and a SQL Agent Proxy.

**Credentials**
  
The first step is to set up a security Credential on the SQL Server that executes the jobs. These can be found under Security>Credentials in SSMS (see Figure 1). The Credential identity should be set to be the name (including the domain)of the Service Account, and must include the password. To create the Credential in T-SQL:

```sql
CREATE CREDENTIAL < CredentialName >
	WITH IDENTITY = '<domain>\<service account>'
		,SECRET = '<password>';
GO
```

[![Credentials][credentials]](http://www.grapefruitmoon.net/img/wp-content/uploads/2014/01/Credentials.jpg)

[credentials]: /img/wp-content/uploads/2014/01/Credentials.jpg
Figure 1

**Proxies**

Next, a Proxy for SSIS must be created in the SQL Agent. These are found in SQL Server Agent>Proxies>SSIS Package Execution (Figure 2). To create a proxy in T-SQL (note that subsystem_id 11 specifies that it is for SSIS Package Execution):

```sql
USE msdb;
GO

EXEC msdb.dbo.sp_add_proxy @proxy_name = N'<ProxyName>'
	,@credential_name = N'<CredentialName>'
	,@enabled = 1;
GO

EXEC msdb.dbo.sp_grant_proxy_to_subsystem @proxy_name = N'<ProxyName>'
	,@subsystem_id = 11;
GO
```

[![Proxy][proxy]](http://www.grapefruitmoon.net/img/wp-content/uploads/2014/01/Proxy.jpg)

[proxy]: /img/wp-content/uploads/2014/01/Proxy.jpg
Figure 2

**Agent Job**
  
Finally, the Agent job needs to be set up to run as the Proxy. In the Run As drop-down in the relevant Job Step, select the newly created Proxy (see Figure 3).

[![Agent][agent]](http://www.grapefruitmoon.net/img/wp-content/uploads/2014/01/Agent.jpg)

[agent]: /img/wp-content/uploads/2014/01/Agent.jpg
Figure 3


**References**

[Credentials](http://technet.microsoft.com/en-us/library/ms161950.aspx)

[Create a SQL Agent Proxy](http://technet.microsoft.com/en-us/library/ms175834.aspx)

[sp_add_proxy](http://technet.microsoft.com/en-us/library/ms188763.aspx)

[sp_grant_proxy_to_subsystem](http://technet.microsoft.com/en-us/library/ms186760.aspx)