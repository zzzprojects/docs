---
PermaID: 101000
Name: Overview
IsHome: 1
---

# Overview

Database applications often rely on Dynamic SQL queries that are constructed at run-time through program logic. The LINQ infrastructure supports similar capabilities through expression trees which are an appropriate abstraction for a variety of scenarios, but for others, a string-based representation may be more convenient. 

 - The dynamic query library relies on a simple expression language for formulating expressions and queries in strings.
 - It provides you with string-based extension methods that you can pass any string expression into instead of using language operators or type-safe lambda extension methods.
 - It is simple and easy to use and is particularly useful in scenarios where queries are completely dynamic, and you want to provide an end-user UI to help build them. 

Using a dynamic LINQ library, we can do the following,

 - Dynamic parsing of strings to produce expression trees such as `ParseLambda` and `Parse` methods
 - Dynamic creation of “Data Classes” using the `CreateClass` method.
 - Dynamic string-based querying through LINQ providers.

## Install Dynamic LINQ Library 

To install Dynamic LINQ (`System.Linq.Dynamic.Core`) library, right-click on your project in Solution Explorer, and select **Manage NuGet Packages…**, and install the following package.

 - System.Linq.Dynamic.Core 

You can also install this library using the **Package Manager Console** window by executing the following command.

`PM> Install-Package System.Linq.Dynamic.Core`

You are ready to start writing some code using the dynamic LINQ.
