---
Permalink: articles/carloscds-ef-core-power-tools
Title: Generate EF Core Classes from Databases with EF Core Power Tools
MetaDescription: Learn how to use EF Core Power Tools to generate Entity Framework Core classes from your existing database. This tool simplifies the development process and saves time. Follow our step-by-step guide and start using EF Core Power Tools today!
tags: carloscds article powertools reverse-engineer
OriginalLink: https://carloscds.net/2018/05/ef-core-powertools/
CreatedDate: 2018-05-28
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
LastMod: 2023-02-20
---

# How to Use EF Core Power Tools to Generate EF Core Classes from Databases

Already using ASP.NET Core? Entity Framework Core? This new version of EF Core is very good, and help in the development process. Today I will talk about the EF Core Power Tools.

But how does this tool help me? With it, you can take an existing database and perform a `Reverse Engineer`, which means generating all classes that you will use in Entity Framework from your database!

Remember, you need at least [Visual Studio 2017](https://visualstudio.microsoft.com/vs/) to use EF Core.

## Installing the component:

Let's start by downloading EF Core Power Tools from this address: [https://github.com/ErikEJ/EFCorePowerTools/wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki)

**TIP**: At the time of writing this article, the standard version has a bug in the generation of the visual model, so I suggest you get the [daily build](https://vsixgallery.com/extensions/f4c4712c-ceae-4803-8e52-0e2049d5de9f/extension.vsix) version.

Power Tools is an extension for Visual Studio, so after downloading, click and install. Don't forget to restart Visual Studio to complete the installation!

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb.png" width="351" height="269" alt="EF Core Powertools">

In my case, I have Visual Studio 2017 Preview too.

## Creating our sample project:

Once installed, we will create a .NET Core Console project:

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb-1.png" width="569" height="397" alt="EF Core Powertools-1">

Now we need to add EntityFramework Core to our project. We will do this using the NuGet Package Manager Console, with the command:

```package-manager
PM> Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb-2.png" width="580" height="57" alt="EF Core Powertools-2">

Now we can use EF Core Power Tools by right-clicking on the project, where we will see the menu below:

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb-3.png" width="458" height="271" alt="EF Core Powertools-3">

We will then choose the `Reverse Engineer` option, and by clicking on the `Add` button you can choose the database: (in my example I will use NorthWind):

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb-4.png" width="447" height="259" alt="EF Core Powertools-4">

Select the tables/views and click `OK`:

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb-5.png" width="368" height="366" alt="EF Core Powertools-5">

Finally, you can adjust your model's settings, such as Context name, Namespace, etc:

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb-6.png" width="367" height="490" alt="EF Core Powertools-6">

That done, we will have our classes and the context in the project:

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb-7.png" width="365" height="401" alt="EF Core Powertools-7">

Now use it, but first check your context, because the connection string with the database was placed there in the code (in my example NorthwindContext.cs), which is not a good practice:

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb-8.png" width="955" height="127" alt="EF Core Powertools-8">

A very nice feature of this tool is the database diagram, which you generate in the context menu:

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb-9.png" width="244" height="163" alt="EF Core Powertools-9">

This option generates a `.dgml` file with the diagram:

<img src="https://carloscds.net/wp-content/uploads/2018/05/image_thumb-10.png" width="728" height="345" alt="EF Core Powertools-10">

Remembering that this tool is open source and is on GitHub: https://github.com/ErikEJ/EFCorePowerTools