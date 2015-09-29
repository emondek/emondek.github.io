---
layout: post
title: Azure Usage Analytics Report - September 2015 Update
---

This is just a minor update to the report.  A number of users have started receiving the following error:

![Consumed Quantity Error](/images/consumed_quantity_error.png)

The September 2015 update has a small change in the Power Query to prevent this error.

You can download the latest report here - [Azure Usage Analytics Report Sept 2015 Update](/files/Azure%20Usage%20Analytics%20(API)%20Sept%202015.xlsx).

Here's what happened...

The GetUsageList API returns a list of all reports by month.  Each report in the list has a link to the GetUsageByMonth API for that month.  The Power Query in this spreadsheet retrieves all detailed usage reports across all months and combines them into a single set of data that it loads into a data model.  Since some of the column names changed in July 2015, monthly reports prior to July use an old set of column names.  When combining these monthly reports, if Power Query used the old column names as column headers then the above error got thrown.  To prevent this from happening, I added a step in the Power Query to sort the monthly reports from newest to oldest before combining them.  This ensures that the latest column names are used in the data model.  I'm not sure if I got lucky in previous versions of the spreadsheet or if something changed in the Billing API but either way this addresses the problem.

Cheers!
