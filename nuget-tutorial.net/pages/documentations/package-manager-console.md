---
PermaID: 100009
Name: Package Manager Console
---

# Package Manager Console

The NuGet Package Manager Console is built into Visual Studio on Windows version 2012 and later. It is not included with Visual Studio for Mac or Visual Studio Code. 

## Download and Installation

In Visual Studio 2017, NuGet and the NuGet Package Manager are automatically installed when you select any .NET-related workloads; you can also install it individually by checking the **Individual components > Code tools > NuGet package manager** option in the Visual Studio 2017 installer.

 - You can use NuGet PowerShell commands to find, install, uninstall, and update NuGet packages. 
 - To open the console in Visual Studio, go to the main menu and select **Tools > NuGet Package Manager > Package Manager Console** command. 

By default, console commands operate against a specific package source and project as set in the control at the top of the window.

<img src="https://raw.githubusercontent.com/zzzprojects/nuget-tutorial/master/docs/images/package-manager-console.png" alt="Package manager console">

## Install Package

To install a package, use `Install-Package` command.

### Examples

The following command adds the `Z.EntityFramework.Classic` package to the default project as specified in the console's project selector.
 
```csharp
PM> Install-Package Z.EntityFramework.Classic
```

The following command adds the Z.EntityFramework.Classic package to a project named EFDemo that is not the default

```csharp
PM> Install-Package Z.EntityFramework.Classic -ProjectName EFDemo
```

When you install a package, it performs the following actions.

 - The console displays applicable license terms in its window with an implied agreement. 
 - If you do not agree to the terms, you should uninstall the package immediately.
 - A reference to the package is added to the project file and appears in Solution Explorer under the References node.

## Uninstall Package

To uninstall a package, use `Uninstall-Package` command.

### Examples

The following command uninstalls the Elmah package from the default project.

```csharp
Uninstall-Package Elmah
```

The following command uninstalls the Elmah package and all its unused dependencies.

```csharp
Uninstall-Package Elmah -RemoveDependencies 
```

The following command uninstalls the Elmah package even if another package depends on it.

```csharp
Uninstall-Package Elmah -Force
```

When you uninstall a package, it performs the following actions.

 - Removes references to the package from the project and references no longer appear in Solution Explorer. 
 - Reverses any changes made to app.config or web.config when the package was installed.
 - Removes previously-installed dependencies if no remaining packages use those dependencies.
