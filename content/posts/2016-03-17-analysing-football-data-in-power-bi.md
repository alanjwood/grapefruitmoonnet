---
title: Analysing Football Data in Power BI
author: alan
type: post
date: 2016-03-17T17:08:54+00:00
url: /analysing-football-data-in-power-bi/
categories:
  - SQL Server
tags:
  - dax
  - football
  - power bi
  - powerpivot
  - powerquery

---
Two passions of mine that go hand in hand are sports and data. Fans nowadays can access a whole host of statistics for every sport imaginable. This data can be used for any number of reasons, including for gambling purposes and to help win fantasy leagues. Most premier sports teams now employ data analysts to look for something that will give them an edge and an advantage over their opponents. (On a related note, I'm currently reading [Big Data Baseball](http://amzn.to/1LsK7gN) by Travis Sawchik - a really interesting insight into how the Pittsburgh Pirates used previously untapped data to improve their fortunes).

For the past year or so, I've been collecting, cleaning, transforming and analysing football results data, mainly for leagues in Scotland and England. I've been using a few of my favoured tools to do this - namely Power BI, PowerPivot and R. The ability to embed reports in a web page was added in a recent Power BI update, so I can now post an example of the kind of thing I've been building. Power Query is used to consume raw data from CSV files and scrape data from the web, then cleanse and shape it into a useable format. Relationships are then created in Power BI and I've added a set of useful measures and calculations using DAX. See below for my interactive Scottish Premiership report, showing a small subset of data going back to the 2000-2001 season.
  
{{< powerbi "https://app.powerbi.com/view?r=eyJrIjoiOTI1N2MyNjUtZTQ0MC00YjM2LThkOGYtN2FlMTY0OWJkOTllIiwidCI6ImRkZTlkYzcxLTJhMmEtNGRmYS1iMTE0LTc5MmZiNzJlYTBlMiIsImMiOjh9" >}}


This report currently allows a user to view league tables; to analyse shots, shots on target and goals; to view historical team matchups and to analyse monthly team form.

Please give it a go, have a play - any comments and suggestions are welcome. Let me know if you spot any errors! I'll be adding more analysis to this over time, as well as adding in data from other leagues across Europe and lower league data.

Sources of the data for this include:
  
[Football-Data.co.uk](http://www.football-data.co.uk/)
  
[Wikipedia](https://en.wikipedia.org/wiki/Main_Page)
  
[SPFL](http://spfl.co.uk/)