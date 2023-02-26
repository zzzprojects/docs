---
Permalink: articles/carloscds-ef6-ngen-to-improve-performance
Title: Title: Improve Entity Framework Performance with NGen
MetaDescription: Learn how to use NGen to pre-compile Entity Framework assemblies and improve the performance of your .NET applications.
LastMod: 2023-02-24
tags: carloscds article ngen
OriginalLink: https://carloscds.net/2014/04/melhorando-a-performance-do-entity-framework-com-ngen/
CreatedDate: 2014-04-21
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# Improve Entity Framework Performance with NGen

In another post, I introduced very quickly about [NGen](/articles/carloscds-ef6-ngen-to-improve-load-time), which serves to pre-compile an application's assemblies, doing the work of JIT (Just in Time Compiler) in advance.

Just to remind you a little, the figure below shows the process of running an application in .NET:

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb5.png" width="612" height="455" alt="Entity Framework performance with NGen-1">

In a natural process of development in .NET, what happens is basically what is in the figure above, you write your code, compile it, and then execute it. But in execution, JIT comes into play, which takes the generated code (Assembly) and translates it into native code, or machine language, which will then be executed by the processor.

During the installation of the .NET Framework on a computer, this process is performed for all .NET components. The .NET core is automatically precompiled on your computer, but all the code you produce does not go through this process.

As I mentioned in the [previous post](/articles/carloscds-ef6-ngen-to-improve-load-time), since version 6, Entity Framework works separately from .NET, through `EntityFramework.dll`, which is usually downloaded by [NuGet](https://nuget-tutorial.net/) inside Visual Studio. This process simply downloads the DLL from the NuGet repository and copies it to a folder in your project. That is great and works great!

Many people complain on forums and blogs about the initial load time of the Entity Framework, when the first command executed, whatever it may be, takes a while, but all subsequent instructions are very quick. Of course, the design of the model and the command can influence this time, but one thing that affects a lot is the lack of precompilation.

Let's see a straightforward example. I will create a Console project in C # and do reverse engineering using the Entity Framework Power Tools, which I have already explained in a previous [post](articles/carloscds-ef6-code-first-from-database). So to accompany me, create a Console type project in Visual Studio, as shown in the figure below:

<img src="https://carloscds.net/wp-content/uploads/2014/04/SNAGHTML4098cce_thumb1.png" width="836" height="479" alt="Entity Framework performance with NGen-2">

Now let's reverse engineer the `Northwind` database, but you can use any other database you have. 

**PS**: Don't forget to install EntityFramework using NuGet: `Install-Package EntityFramework`.

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb6.png" width="830" height="238" alt="Entity Framework performance with NGen-3">

Now I will choose the database and generate the classes.

To show the difference in performance, I will create a simple query in EF, and to mark the time, we will use the class StopWatch (). See below:

```csharp
static void Main(string[] args)
{
    Stopwatch clock = new Stopwatch();

    clock.Start();
 
    var db = new NorthwindContext();

    var datas = db.Customers;

    foreach(var c in datas)
    {
        Console.WriteLine(c.CompanyName);
    }
    
    clock.Stop();

    Console.WriteLine("\n\nQuery Time: {0} segs.\n\n", clock.Elapsed.TotalSeconds);
}
```

See the result:

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb2.png" width="716" height="191" alt="Entity Framework performance with NGen-4">

Now we are going to use NGen in this project and retake the test. To do this, open a Visual Studio command prompt (as an administrator) and run the command below:

```package-manager
ngen Install EFPerformance.exe
```

You see something like this:

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb7.png" width="885" height="206" alt="Entity Framework performance with NGen-5">

Notice that in addition to our project (`EFPerformance.exe`) the EntityFramework dll was also compiled.

Now let's run the program again, straight from the command line and look at the speed difference:

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb4.png" width="655" height="194" alt="Entity Framework performance with NGen-6">

Realize that it got extremely faster !!!

Now, if you have a web application, you can improve performance using the command of [aspnet_compiler](https://docs.microsoft.com/en-us/previous-versions/ms229863(v=vs.100)). Still, it does not generate native images like NGen, despite increasing performance by precompiling some project files (`.aspx`, `.ascx`, `app_code`, etc.). If you prefer, the Visual Studio IDE has this option when publishing the web application:

<img src="https://carloscds.net/wp-content/uploads/2014/04/SNAGHTML41af2f8_thumb.png" width="660" height="518" alt="Entity Framework performance with NGen-7">

I hope you liked the tips!