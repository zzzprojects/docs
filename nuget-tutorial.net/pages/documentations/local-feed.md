---
PermaID: 100004
Name: Local Feed
---

# Local Feed

Local NuGet package feeds are simply hierarchical folder structures on your local computer or network in which you place packages. 

<img src="https://raw.githubusercontent.com/zzzprojects/nuget-tutorial/master/docs/images/host-nuget-packages.png" alt="Host NuGet packages-1">

 - NuGet creates this structure automatically when you use the `nuget add` command to copy a package to the feed.
 - These feeds can then be used as package sources with all other NuGet operations using the CLI, the Package Manager UI, and the Package Manager Console.

The following command adds a package to our local feed.

```csharp
nuget add SuperLogger.1.0.0.nupkg -source D:\MyPackages
```

It will also display the results of the above command.

<img src="https://raw.githubusercontent.com/zzzprojects/nuget-tutorial/master/docs/images/host-nuget-packages1.png" alt="Host NuGet packages-2">

The following hierarchical folder structure is also created.

<img src="https://raw.githubusercontent.com/zzzprojects/nuget-tutorial/master/docs/images/host-nuget-packages2.png" alt="Host NuGet packages-3">

## Multiple Packages

The `nuget add` command works with one package at a time, but it won't work when you to want to copy multiple packages.

```csharp
nuget init <source> <destination> [options]
```
For multiple packages, you can use `nuget init` which copies all the packages from a `source` folder to a `destination` folder using the same hierarchical layout as mentioned above for a single package with `nuget add`.

```csharp
nuget init C:\Packages D:\MyPackages
```

The `init` command creates a folder for each package identifier, each of which contains a version number folder, within which is the appropriate package.

