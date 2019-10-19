---
PermaID: 1000201
Name: create-mvc-project
---

# Create MVC Project 

In this article, we will learn Dapper CRUD (Create, Retrieve, Update, Delete) functionality in the ASP.NET MVC application. The easiest way to create an ASP.NET application is to open Visual Studio and create a C# web project using the ASP.NET Web Application (.NET Framework) template. 

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/create-mvc-project-1.png">

Name the project **DapperDemoApp** and select **OK**.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/create-mvc-project-2.png">

In **New ASP.NET Web Application - DapperDemoApp**, select **MVC**.

## Install Dapper

Dapper is available as a nuget package and you can install it using **Nuget Package Manager**.

In the **Package Manager Console** window, enter the following command.

```csharp
PM> Install-Package Dapper
```

You can also install Dapper by selecting the project in Solution Explorer. 

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/create-mvc-project-3.png">

Right-click on your project and select **Manage Nuget Packages...**.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/create-mvc-project-4.png">

Search for Dapper and install the latest version by pressing the install button. You are now ready to start your application.
