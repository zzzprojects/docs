---
Name: Export Data to Csv Files
---

# Export Data to Csv Files

## Description

Creating a CSV file could be tiresome; **Effort** provides an easy-to-use program, called [**Effort.CsvTool**](https://github.com/zzzprojects/EntityFramework-Effort/tree/master/Main/Source/misc/Effort.CsvTool) that exports the current state of your entire database into CSV files that Effort can consume. 

It is a GUI based tool.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-Effort/master/docs/images/csv-tool-1.png" alt="csv tool-1">

## Providers

This tool supports the following database providers.

 - Odbc Data Provider
 - OleDb Data Provider
 - OracleClient Data Provider
 - SqlClient Data Provider
 - Microsoft SQL Server Compact Data Provider

The provider can be selected from the Provider dropdown list, and you can set different property values for any provider such as the connection string of your database, etc.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-Effort/master/docs/images/csv-tool-2.png" alt="csv tool-2">

In the **Export path** field, you can specify the directory where you want to save all the **CSV** files. Click **Export** button and you are done.

## Note:

The content of each database table is represented by a dedicated CSV file that has to be named as **{table name}.csv**.



