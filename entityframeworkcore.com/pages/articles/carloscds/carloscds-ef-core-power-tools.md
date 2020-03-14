---
Permalink: articles/carloscds-ef-core-power-tools
name: EF Core - Power Tools
tags: carloscds article powertools reverse-engineer
OriginalLink: http://carloscds.net/2018/05/ef-core-powertools/
CreatedDate: 2018-05-28
CreatedUserName: Carlos dos Santos
CreatedUserLink: http://carloscds.net/
---

# EF Core - Power Tools

Already using ASP.NET Core? Entity Framework Core? This new version of EF Core is very good, and help in the development process. Today I will talk about the EF Core Power Tools.

But how does this tool help me? With it, you can take an existing database and perform a `Reverse Engineer`, which means generating all classes that you will use in Entity Framework from your database!

Remember, you need at least [Visual Studio 2017](https://visualstudio.microsoft.com/vs/) to use EF Core.

## Installing the component:

Let's start by downloading EF Core Power Tools from this address: [https://github.com/ErikEJ/EFCorePowerTools/wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki)

**TIP**: At the time of writing this article, the standard version has a bug in the generation of the visual model, so I suggest you get the [daily build](http://vsixgallery.com/extensions/f4c4712c-ceae-4803-8e52-0e2049d5de9f/extension.vsix) version.

Power Tools is an extension for Visual Studio, so after downloading, click and install. Don't forget to restart Visual Studio to complete the installation!

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb.png" width="351" height="269">

In my case, I have Visual Studio 2017 Preview too.

## Creating our sample project:

Once installed, we will create a .NET Core Console project:

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb-1.png" width="569" height="397">

Now we need to add EntityFramework Core to our project. We will do this using the NuGet Package Manager Console, with the command:

```package-manager
PM> Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb-2.png" width="580" height="57">

Now we can use EF Core Power Tools by right-clicking on the project, where we will see the menu below:

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb-3.png" width="458" height="271">

We will then choose the `Reverse Engineer` option, and by clicking on the `Add` button you can choose the database: (in my example I will use NorthWind):

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb-4.png" width="447" height="259">

Select the tables/views and click `OK`:

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb-5.png" width="368" height="366">

Finally, you can adjust your model's settings, such as Context name, Namespace, etc:

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb-6.png" width="367" height="490">

That done, we will have our classes and the context in the project:

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb-7.png" width="365" height="401">

Now use it, but first check your context, because the connection string with the database was placed there in the code (in my example NorthwindContext.cs), which is not a good practice:

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb-8.png" width="955" height="127">

A very nice feature of this tool is the database diagram, which you generate in the context menu:

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb-9.png" width="244" height="163">

This option generates a `.dgml` file with the diagram:

<img src="http://carloscds.net/wp-content/uploads/2018/05/image_thumb-10.png" width="728" height="345">

Remembering that this tool is open source and is on GitHub: https://github.com/ErikEJ/EFCorePowerTools