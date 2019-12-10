# Overview

## Description

LINQ to SQL is a component of .NET Framework that provides a run-time infrastructure for managing relational data as objects. In LINQ to SQL, you can access SQL databases just like accessing an in-memory collection by using the LINQ technology.

 - The data model of a relational database is mapped to an object model expressed in the programming language of the developer. 
 - When the application runs, LINQ to SQL translates the LINQ queries into SQL the and sends them to the database for execution. 
 - When the database returns the results, LINQ to SQL translates them back to objects that you can work with in your own programming language.

## What is LINQ

LINQ (Language Integrated Query) is a structured query syntax in .NET used to save and retrieve data from different sources. 

 - It provides a single querying interface for different types of data sources such as Object Collection, SQL server database, XML, web service etc. 
 - LINQ gives us strongly typed compile time checks on queries that can execute against in memory data, relational data, and XML data.

## LINQ to SQL Capabilities

LINQ to SQL has all the capabilities you would expect as a SQL developer. You can query, insert, update, and delete data from database tables using LINQ to SQL.

## Limitations

There are few limitations when you use LINQ to SQL;

 - It can only be used with SQL Server as backend.
 - It requires at least .NET 3.5
 - Tables are mapped strictly on a 1:1 basis (one table = one class)
