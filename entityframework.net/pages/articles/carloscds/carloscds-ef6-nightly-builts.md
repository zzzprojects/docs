---
Permalink: articles/carloscds-ef6-nightly-builts
Title: Installing and Accessing Entity Framework 6 Nightly Builds
MetaDescription: Learn how to install Entity Framework 6 and access its nightly builds to explore its new features before they are officially released. Access the latest version and explore its source code.
LastMod: 2023-02-24
tags: carloscds article nightly-built
OriginalLink: https://carloscds.net/2013/08/entity-framework-6trabalhando-com-os-nightly-builds/
CreatedDate: 2013-08-18
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# Installing and Accessing Entity Framework 6 Nightly Builds

Now returning to write more about the Entity Framework, we are waiting for the new version 6 (still with no definitive date to be released) that will bring very significant improvements, and we will explore some of them in the next articles.

The first thing is how to install EF 6 on a project. Usually, you use the Nuget Package Console to add EF to your project, with the following command: (open the Tools -> Library Package Manager -> `Package Manager Console`)

```package-manager
PM> InstallPackage EntityFramework
```

But it will install EF 5, which is the latest production version, released alongside Visual Studio 2012 and .Net 4.5. But if you want to install EF 6 in a project, you will have to use the following command:

```package-manager
PM> InstallPackage EntityFramework -Pre
```

That will install the latest published version of EF 6, which is currently Alpha 1. Cool, this allows you to work with EF 6 and explore some very interesting features, but we don't want this, we want the cool things not yet published.

## Accessing Night Builds

EF 6, as everyone should know, is Open Source and all its source code, I really said all the source code, can be downloaded at https://github.com/dotnet/ef6. I recommend that you download the code and take a look at how the project is structured, how the unit tests are. The source code of EF is, without a doubt, an excellent source of studies.

Although EF 6 is entirely open source, only Microsoft releases the official versions and also the so-called Nightly Builds or Night Builds, which are nothing more than a daily version of what is being developed by Microsoft and the community that is collaborating on the project. If you are, like me, a person who likes the news a lot and is eager to try it, these nightly builds give us this.

To access Nightly Builds, you need to configure Visual Studio to access the address where they are published daily. We will do this by accessing the NuGet configuration in Tools / Library Package Manager / Package Manager Settings and then in Package Source:

<img src="https://www.carloscds.net/wp-content/uploads/2013/08/SNAGHTMLc0f2ab2_thumb2.png" width="679" height="425" alt="Package source">

Click on the "+" button and then fill in the fields:

Name: Nightly Builds
Source: https://www.myget.org/F/aspnetwebstacknightly/

Save by clicking `OK`. Now that we have set it up, let's create a Console type project to be able to add the latest published version. To do this, create the Console type project, choosing .Net 4.5 because some new features will only work in this version of .NET.

With the project created, right-click on the name of your solution and then choose Manage Nuget Packages and you will see the image below:

<img src="https://www.carloscds.net/wp-content/uploads/2013/08/SNAGHTMLc1440a3_thumb2.png" width="628" height="420" alt="Manage NuGet packages-1">

See that we already have the Nightly Builds in our window, but first, we still need to indicate that we want the "Pre Releases", which are the unofficial versions. You can do this by clicking on the “Stable Only” option and changing to "Include Prerelease".

With this done, we will click on "Nightly Builds", and in the search window we will type “Entity” and see that you will see the latest version published, in my case on 2013-08-18:

<img src="https://www.carloscds.net/wp-content/uploads/2013/08/SNAGHTMLc172099_thumb2.png" width="669" height="448" alt="Manage NuGet packages-2">

Now just click on "Install" to install the latest version.

A list of new features can be found at: https://docs.microsoft.com/en-us/ef/ef6/what-is-new/past-releases

In the next post, we will explore in more detail some of these new features!