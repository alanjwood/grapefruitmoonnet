---
title: SSIS – Change the Protection Level for all Packages in a Project
author: alan
type: post
date: 2014-08-04T15:15:14+00:00
url: /ssis-change-the-protection-level-for-all-packages-in-a-project/
categories:
  - SQL Server
tags:
  - biml
  - sql
  - ssis

---
Here's a quick one I found this morning while fixing an execution issue with one of my SSIS packages. I needed to change the Protection Level of all the packages in a project from _Encrypt With User Key_ to _Don't Save Sensitive_. The project contains over 30 packages, and I didn't really fancy opening each one, navigating to the properties and amending the Protection Level for each one - that's too time-consuming and frankly, boring. I initially figured I'd write a PowerShell or Python script to amend the relevant XML in each of the .dtsx files. But a quick Google came up with [this MS Technet page](http://technet.microsoft.com/en-us/library/cc879310.aspx), which details how to make the change via the command line, for both individual packages and for all packages within a project.

A quick amendment to the command to use the value for DontSaveSensitive (0) and to remove the password parameter was all that was required, so running this:

```
for %f in (*.dtsx) do dtutil.exe /file %f /encrypt file;%f;0
```

...and pressing Y to confirm each change as prompted, resulted in the change being made to all packages. Result! Well almost. After going back to Visual Studio and changing the Protection Level of the Project (it must match that of the Packages), I tried to build the solution. It returned an error stating that for all the Packages, the Protection Level was incorrect and had to match the Project. But I'd just changed all of that! Turns out I had to open all the packages in Visual Studio (for some reason), and then it would build without making any further changes. Odd.

While this is useful to know for future reference, I'm much more likely to be developing SSIS projects using [BIML](http://bimlscript.com/), which would make changes like this far easier, as it would be a simple change in the BIML template, re-generate the packages and job done. It's the future...