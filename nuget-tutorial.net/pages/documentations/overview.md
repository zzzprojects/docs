---
PermaID: 100000
Name: Overview
IsHome: 1
---

# Overview

## What is NuGet?

A tool for any modern development platform and is a mechanism through which developers can create, share, and consume useful code. Often such code is bundled into **packages** that contain compiled code (as DLLs) along with other content needed in the projects that consume these packages.

 - For .NET and .NET Core, the Microsoft-supported mechanism for sharing code is NuGet, which defines how packages for .NET are created, hosted, and consumed, and provides the tools for each of those roles.
 - A NuGet package is a single ZIP file with the `.nupkg` an extension that contains compiled code (DLLs), other files related to that code, and a descriptive manifest that includes information like the package's version number. 

## Workflow

 - Developers with code to share create packages and publish them to a public or private host. 
 - Package consumers obtain those packages from suitable hosts, add them to their projects, and then call a package's functionality in their project code. 
 - NuGet itself then handles all of the intermediate details.

<img src="https://raw.githubusercontent.com/zzzprojects/nuget-tutorial/master/docs/images/workflow.png">

 - A host serves as the point of connection between package creators and package consumers. 
 - Creators build useful NuGet packages and publish them to a host. 
 - Consumers then search for useful and compatible packages on accessible hosts, downloading and including those packages in their projects. 
 - Once installed in a project, the packages' APIs are available to the rest of the project code.

## Managing Dependencies

One of the most powerful features of a package management system is to easily build on the work of others. NuGet also manages dependency **tree** or **graph** on behalf of a project. 

 - You need only concern yourself with those packages that you're directly using in a project. 
 - If any of those packages themselves consume other packages which can consume others packages, NuGet takes care of all those down-level dependencies.