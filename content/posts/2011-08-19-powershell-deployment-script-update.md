---
title: Powershell Deployment Script Update
author: alan
type: post
date: 2011-08-19T17:26:22+00:00
url: /powershell-deployment-script-update/
posterous_02249d33a2405d1eae69ce626eb5a028_post_id:
  - 66162320
posterous_02249d33a2405d1eae69ce626eb5a028_permalink:
  - http://sqlserver.posterous.com/powershell-deployment-script-update
categories:
  - SQL Server
tags:
  - deployment
  - powershell
  - ssis

---
Since last weeks [post on automated DB and ETL deployment builds with Powershell][1], I’ve made a couple of small but useful changes to the Powershell script.

The distinct parts of the script are now encapsulated in parameterised functions. The end goal of this is to be able to turn the main script into a Powershell Module that could be installed and used when required. So now there are three functions, GetHeadFromSVN, SyncDB and DeployPackages.

DeployPackages has been changed to make it quicker and more streamlined. It will now only deploy or re-deploy packages that have been changed in the last hour, rather than every package in the repository.

The functions are as follows:

```
# Get latest DB scripts and SSIS packages from SVN
function GetHeadFromSVN
{
    param($svnpath, $svnurl, $destinationfolder)

    Set-Location $svnpath
    svn checkout $svnurl $destinationfolder
}

# Use Red Gate SQL Compare to deploy schema changes to each DB
function SyncDB
{
    param($sqlcomparelocation, $argfile)

    Set-Location $sqlcomparelocation

    Invoke-Expression ".sqlcompare /ArgFile:$argfile"
}

# Deploy SSIS packages
function DeployPackages
{
    param($filepath, $ssisfolder, $server, $dtutilpath)

        $dtutil = $dtutilpath
        Set-Location $filepath

       # Only deploy packages that have been changed in the last hour
        $files=get-childitem *.dtsx | where {$_.LastWriteTime -gt (Get-Date).AddHours(-1)}

        if ($files -ne $null) {
        foreach ($file in $files) {
            $basename=$file.BaseName
            $deletearguments = " /delete /SQL $ssisfolder/$basename /SourceServer $server"
            $deployarguments = " /file $file /copy SQL``;$ssisfolder/$basename /destserver $server"

            Set-Location $dtutil

            Invoke-Expression "dtutil $deletearguments"
            Invoke-Expression "dtutil $deployarguments"
         }
    }
}
```

Here are some examples of how they would be called:

```
GetHeadFromSVN "C:Program FilesSlikSvnbin" http://<svnserver>/repos/bi/Database "C:NightlyBuildsBISVNDatabase"
GetHeadFromSVN "C:Program FilesSlikSvnbin" http://<svnserver>/repos/bi/ETL/branches/Phase2 "C:NightlyBuildsBISVNETL"

SyncDB "C:Program FilesRed GateSQL Compare 9" "C:NightlyBuildsBIConfigurationStagingCompare.xml"

DeployPackages "C:NightlyBuildsBISVNETLSourceToStage" "SourceToStage" "<target server>" "C:Program FilesMicrosoft SQL Server100DTSBinn"
DeployPackages "C:NightlyBuildsBISVNETLStageToWarehouse" "StageToWarehouse" "<target server>" "C:Program FilesMicrosoft SQL Server100DTSBinn"
```

The [Codeplex][2] project page has been updated with the latest code.

 [1]: http://www.grapefruitmoon.net/automating-db-and-ssis-builds-from-source-control/
 [2]: http://bideploy.codeplex.com