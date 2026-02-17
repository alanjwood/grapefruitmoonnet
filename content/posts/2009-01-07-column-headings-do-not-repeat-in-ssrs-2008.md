---
title: Column headings do not repeat in SSRS 2008
author: alan
type: post
date: 2009-01-07T15:18:00+00:00
url: /column-headings-do-not-repeat-in-ssrs-2008/
categories:
  - SQL Server
tags:
  - bugs
  - Reporting Services
  - SSRS

---
I've just stumbled upon an odd issue with the Tablix control in SSRS 2008.  I deployed a report on a server a few weeks ago and it is now reporting on enough data to make it span multiple pages.  I wanted the column headings to appear on all pages, not just the first.  However, when I set the RepeatColumnHeaders property on the Tablix to be true, the headings still did not appear on the next page.  After a bit of playing around I enabled Advanced Mode in the Grouping Pane, and had a look at the properties for the top (Static) group in Row Groups.  Under Other, there is a property called RepeatOnNewPage.  Setting this to True made the column headers appear as required.  OK, so that's fine, but I can't help but think that this a a bug in SSRS 2008, and should be fixed by MS.  Looking through the RDL file itself, I can't find anything that would cause the RepeatColumnHeaders property not to behave as expected.

The "Header should remain visible while scrolling" option does not appear to work either; I haven't yet found a workaround for this.

![repeatoption][repeatoption]

[repeatoption]: /img/wp-content/uploads/2009/01/repeatoption1.jpeg