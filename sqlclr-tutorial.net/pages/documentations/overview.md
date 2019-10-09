---
PermaID: 100000
Name: Overview
IsHome: 1
---

# Overview

## What is CLR?

The CLR stands for common language runtime and is the heart of the Microsoft .NET Framework. It provides the execution environment for all .NET Framework code. The CLR provides various functions and services required for program execution, such as;

 - Just-in-time (JIT) compilation
 - Allocating and managing memory
 - Enforcing type safety
 - Exception handling 
 - Thread management, and security, etc. 

## What is SQL CLR?

SQL CLR is technology for hosting of the Microsoft .NET common language runtime engine within SQL Server. It was introduced in Microsoft SQL Server 2005.

 - Code that runs within the CLR is referred to as managed code. 
 - SQL CLR allows managed code to be hosted by, and run in the Microsoft SQL Server environment.

### CLR Object Types

SQL CLR allows the following types of managed code objects in SQL Server in .NET languages (C# or VB.NET).

 - **Stored procedures:** Analogous to procedures or void functions in procedural languages.
 - **Triggers:** Stored procedures that fire in response to Data Manipulation Language (DML) or Data Definition Language (DDL) events.
 - **User-defined Functions:** Analogous to functions in procedural languages.
 - **User-defined Aggregates:** Allow developers to create custom aggregates that act on sets of data instead of one row at a time.
 - **User-defined Types:** Allow users to create simple or complex data types which can be serialized and deserialized within the database.

The managed code compiles to native code before execution; you can achieve significant performance increases in some scenarios.

## Enabling CLR Support

By default, the CLR support in the SQL Server database engine is disabled. To enable CLR support, you need to set the `clr enabled` option of `sp_configure` system stored procedure to 1.

```sql
sp_configure 'clr enabled', 1
GO
RECONFIGURE
GO
```

You can disable CLR integration by setting the `clr enabled` option to 0. When you disable CLR integration, SQL Server stops executing all CLR routines and unloads all application domains.