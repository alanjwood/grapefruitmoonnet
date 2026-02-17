---
title: Automating DB and SSIS builds from source control
author: alan
type: post
date: 2011-08-12T19:16:00+00:00
url: /automating-db-and-ssis-builds-from-source-control/
categories:
  - SQL Server
tags:
  - deployment
  - powershell
  - ssis
  - subversion

---
This week I&rsquo;ve been working on a new branch of a data warehouse project. To make deployment to development environments easier I decided I&rsquo;d attempt to get some sort of continuous integration/automated builds to our dev server up and running. Doing this sort of thing has never been particularly straightforward for database projects, but with the fantastic [SQL Compare][1] command line tools and [SQL Source Control][2] from [Red Gate][3], this task is made so much easier.

I wanted something relatively simple (as always), something that meant not having to install a framework. I did look at [TeamCity][4], [NAnt][5] and [MSBuild][6], but decided against using any of these, at least in the short term. My reasoning being that something light and fairly portable with as few dependencies as possible would be more useful at this stage.

The intention was to set up a nightly build job that would:

  1. Get the latest code from the development branch from SVN
  2. Deploy any schema changes to the development databases
  3. Delete and re-deploy the SSIS packages for the ETL

I used the excellent [Continuous Integration][7] article from Red Gate to get me going. I had started to implement this using a batch file (yuk!), but quickly moved onto using [PowerShell][8], my new favourite toy on Windows! The examples in each section will show the PowerShell commands used in the script.

### Setup and Configuration

Working locally initially, I created the following folder structure on the C drive:

  * NightlyBuilds
  * &mdash; BI (the project name)
  * &mdash;&mdash; Configuration (to hold any configuration files)
  * &mdash;&mdash; SVN (for the SVN checkout)
  * &mdash;&mdash; Output (for any output or report files)

### Source Control

Our development team use dedicated local databases and push changes to the centralised [Subversion][9] (SVN) repository using Red Gate [SQL Source Control][2]. The first step in the build was to check out the latest SQL schema scripts and SSIS packages. After installing the [SlikSVN][10] client, I could use the following Powershell commands to perform the check out:

```
Set-Location "C:Program FilesSlikSVNbin"
svn checkout http:///repos/BI/Database "c:NightlyBuildsBISVNDatabase"
svn checkout http:///repos/BI/ETL/branches/Phase2 "c:NightlyBuildsBISVNETL"
```

### Deploy Schema Changes

Next up was to use the SQL Compare command line tools to compare each of the databases on the development environment with the schema scripts.

```
Set-Location "c:Program FilesRed GateSQL Compare 9"
Invoke-Expression ".sqlcompare /ArgFile:C:NightlyBuildsBIConfigurationEDWCompare.xml"
Invoke-Expression ".sqlcompare /ArgFile:C:NightlyBuildsBIConfigurationStagingCompare.xml"
```

At the moment, I&rsquo;m using XML argument files for each comparison (this may well change as this progresses), these have a format like:

```
C:NightlyBuildsBISVNDatabaseEDWbranchesPhase2
server name
    EDW
default
C:NightlyBuildsBIOutputEDWSchemaDiffReport.html
Interactive
C:NightlyBuildsBIOutputEDWSchemaSyncScript.sql
user
role
```

### Deploy SSIS Packages

With the databases being up to date, the next task was to deploy the SSIS packages. Having experienced a couple of issues in the past with packages not being overwritten correctly, I decided that, to be on the safe side, I&rsquo;d delete an existing package before re-deploying it. The command line tool [dtutil.exe][11] lets us perform these tasks and more, but it can be incredibly frustrating to use at times, with it&rsquo;s inconsistent syntax and problems with differences in SQL Server versions and 32 and 64 bit versions. Using the following function, the path to the SSIS packages is specified and each one is deleted on development, then redeployed.

```
function DeployPackages 
{
    param($filepath, $ssisfolder, $server)

    Set-Location $filepath

    $files=get-childitem *.dtsx

    foreach ($file in $files) {
        $basename=$file.BaseName
        $deletearguments = " /delete /SQL $ssisfolder/$basename /SourceServer $server"
        $deployarguments = " /file $file /copy SQL``;$ssisfolder/$basename /destserver $server"

        Set-Location $dtutil

        Invoke-Expression "dtutil $deletearguments"
        Invoke-Expression "dtutil $deployarguments"
    } 
}

DeployPackages "C:NightlyBuildsBISVNETLSourceToStage" "EDW/SourceToStage" ""
DeployPackages "C:NightlyBuildsBISVNETLStageToWarehouse" "EDW/StageToWarehouse" ""
```

### Improvements

So that, in a nutshell, is the process I&rsquo;m currently using. It works, which is nice, but I have quite a few improvements in mind:

  * Firstly, it seems to be very slow when running dtutil. I&rsquo;m not sure if this is because I&rsquo;m currently doing everything over a VPN, but I&rsquo;ll be looking to improve the general performance. To be honest, it&rsquo;s not a major issue, as it&rsquo;s mainly designed for builds that happen overnight, but it would be nice if I could streamline it. I did try to use the /fdelete switch on dtutil to remove an entire folder, but that demands that the folder is empty.
  * Add more parameterisation so that it could be used for different projects on different environments
  * Use the [SQL Data Compare][12] command line tools to ensure any static/lookup data is up to date
  * Add an option to drop (if required) & create the databases and SSIS folders. This would allow the script to deploy to &ldquo;virgin&rdquo; machines and test the entire build.
  * Add job creation scripts
  * Build in SSRS report deployment. The project I&rsquo;m working on does not use a great deal of SSRS reports (OBIEE is the end user tool), but given that report deployment can be a pain, it would be nice to include this too.
  * Add some reporting as to the success or failure of any component of the script.

**Update:** Looks like the VPN was indeed causing a slowdown. Running it connected locally to the network, 60+ SSIS packages were deployed in <3 minutes.

### Code

I&rsquo;ve started a project on [Codeplex][13] for this, really just as a place to keep the code. Any suggestions or comments are, as always, more than welcome.

 [1]: http://www.red-gate.com/products/sql-development/sql-compare/
 [2]: http://www.red-gate.com/products/sql-development/sql-source-control/
 [3]: http://www.red-gate.com
 [4]: http://www.jetbrains.com/teamcity/
 [5]: http://nant.sourceforge.net/
 [6]: http://msdn.microsoft.com/en-us/library/wea2sca5(v=vs.90).aspx
 [7]: http://www.red-gate.com/products/sql-development/sql-developer-bundle/learn-more/continuous-integration
 [8]: http://technet.microsoft.com/en-us/library/bb978526.aspx
 [9]: http://subversion.tigris.org/
 [10]: http://www.sliksvn.com/en/download
 [11]: http://msdn.microsoft.com/en-us/library/ms162820.aspx
 [12]: http://www.red-gate.com/products/sql-development/sql-data-compare/
 [13]: http://bideploy.codeplex.com/