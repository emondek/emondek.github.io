---
layout: post
title: Azure Usage Analytics Report
---

Analyzing usage data for Microsoft Azure can be very challenging.  There are dozens of different services, each with their own billing meters and rates that can vary by region.  To help simplify this task, I (along with the help of a few colleagues) created a Microsoft Excel-based report that enables you to analyze your Azure usage by department, account, subscription, service, region and time frame.

**NOTE:  This report only works for organizations that have licensed Azure via an Enterprise Program such as a Microsoft Enterprise Agreement.  Licensing Azure via an Enterprise Program provides you access to the Azure Enterprise Portal and the Azure Billing API which are required for this report.**

Here's a link where you can download the report - [Azure Usage Analytics Report](/files/Azure%20Usage%20Analytics%20(API)%20v2.2.xlsx).

**Prerequisites**

- Excel 2013 (or newer)
- Microsoft Power Query for Excel add-in (from Jan. 12th, 2015 or later).  You can download the add-in [here](http://www.microsoft.com/en-us/download/details.aspx?id=39379&CorrelationId=6c71a8d2-343d-43d0-bf65-a77fbf000b47). 
- This report also takes advantage of Power View, Power Pivot, PivotTables and PivotCharts which are all available as part of Office Professional Plus and Office 365 Professional Plus editions, and in the standalone edition of Excel 2013.

**Here are a few steps to help get you started...**

1. Log in to the **Azure Enterprise Portal** ([https://ea.azure.com](https://ea.azure.com)).  If you're not set up as an Azure Enterprise Administrator on this portal then you'll need to work with one of the Enterprise Administrators from your organization.
2. From the home page, grab your **Enrollment Number**

    ![Enrollment Number](/images/enrollment_number.png)

3. Navigate to the **Manage Access** page
4. Scroll down to the bottom of the **Manage Access** page to the **Usage Api Access Key** section and grab the **Primary Key** or the **Secondary Key**.  You may need to generate one of these keys if they don't exist.  A common issue when first using this report is caused by not grabbing the entire key.  Be sure to use Ctrl+A in the **Primary Key** or **Secondary Key** field to select the entire Access Key.  It's a pretty long string.

    ![Access Key](/images/access_key.png)

5. Now open the **Azure Usage Analytics (API).xlsx** file that you downloaded from the [link](/files/Azure%20Usage%20Analytics%20(API)%20v2.2.xlsx) above.
6. When prompted, click **Enable Editing**
7. When prompted, click **Enable Content**
8. FYI, the **Get Started** worksheet contains additional instructions for using the report
9. Navigate to the **Enrollment Info** worksheet.  The **Enrollment Info** worksheet contains a single table with **Enrollment** and **Key** columns.  It only contains a single row of data.  Replace the existing **Enrollment** and **Key** values with your Enrollment Number and Access Key that you retrieved from the Enterprise Portal above.  Make sure to delete all the characters for the existing Access Key and copy all the characters from your Access Key.  It should look like this when you're done but with your own values.

    ![Enrollment Info](/images/enrollment_info.png)

10. Navigate to the **Usage Analysis** worksheet
11. In the Excel Ribbon, select the **Data** tab and hit the **Refresh All** button.
12. If you're prompted to select the authentication type for accessing web content then select **Anonymous** as shown below.  The Azure Billing API uses the access key in the HTTP header for authentication.  It also uses HTTPS to encrypt data in-transit.

    ![Access Web Content](/images/access_web_content.png)

13. If you're prompted to select privacy levels then select **Organizational** as shown below.

    ![Privacy Levels](/images/privacy_levels.png)

14. The Excel status bar down at the bottom will update with messages as it retrieves data from the Azure Billing API.  Don't be surprised if it takes a few minutes to retrieve all your data which may consist of 100K+ rows.
15.  Once your data has been retrieved, the PivotTable, PivotCharts and Slicers on the **Usage Analysis** worksheet will update.  It will look something like this but with your own data.

    ![Usage Analysis](/images/usage_analysis.png)

16. The remaining worksheets leverage Power View to show Usage Trend by Department, Usage Trend by Account, etc.
17. Once you're done analyzing your usage data, save the spreadsheet.  This will save your Enrollment Number and Access Key so that in the future all you have to do is hit **Refresh All** on the **Data** tab to grab your latest usage data.

**Additional Info**

You can get additional info on the Azure Billing API from these docs:

- Technical Spec is available [here](https://automaticbillingspec.blob.core.windows.net/spec/WAEP Functional Design - Automated Billing Access API Tech Spec.docx)
- Sample code is available [here](https://automaticbillingspec.blob.core.windows.net/spec/UsageDownloadRestfulSampleClient.zip)

**NOTE:  This report is a sample of how you can use Excel and the Azure Billing API to analyze your usage data.  It is NOT a supported tool from Microsoft.  But since it is just a sample, feel free to customize it to meet your specific needs.**

Enjoy!

