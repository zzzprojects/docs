---
Permalink: installation-nuget
Name: NuGet
---

# NuGet

## Install Dynamic LINQ Library

To install Dynamic LINQ (`System.Linq.Dynamic.Core`) library, right-click on your project in Solution Explorer, and select **Manage NuGet Packages...**, and install the following package.

- System.Linq.Dynamic.Core

You can also install this library using the **Package Manager Console** window by executing the following command.

`PM> Install-Package System.Linq.Dynamic.Core`

You are ready to start writing some code using the Dynamic LINQ.

This library can be used for

- LINQ to Objects;
- Entity Framework with SQL;
- Any LINQ Provider which implements LINQ Expressions;

## Install EntityFramework versions from the Dynamic LINQ Library

In case you would like to have support for *Async*-operators, you need to install the correct version for your used EntityFramework version.

| EntityFramework version | NuGet needed |
| :--- | :--- |
| EntityFramework | [EntityFramework.DynamicLinq](https://www.nuget.org/packages/EntityFramework.DynamicLinq) |
| EntityFrameworkCore | [Microsoft.EntityFrameworkCore.DynamicLinq](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.DynamicLinq) |
| Z.EntityFramework.Classic | [Z.EntityFramework.Classic.DynamicLinq](https://www.nuget.org/packages/Z.EntityFramework.Classic.DynamicLinq) |
