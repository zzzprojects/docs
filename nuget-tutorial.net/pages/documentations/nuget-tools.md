---
PermaID: 100007
Name: NuGet Tools
---

# NuGet Tools

To work with NuGet, as a package consumer or creator, you can use a command-line interface (CLI) tools as well as NuGet features in Visual Studio.

NuGet provides a variety of tools used by both creators and consumers.

|Tool           |Platforms      |Applicable Scenarios   |Description |
|:------------- |:------------- |:--------------------- |:---------- |
|[nuget.exe CLI](/nuget-cli)    |All            |Creation, Consumption    |Provides all NuGet capabilities, with some commands applying specifically to package creators, some applying only to consumers, and others applying to both. |
|[dotnet CLI](/dotnet-cli)      |All            |Creation, Consumption    |Provides certain NuGet CLI capabilities directly within the .NET Core toolchain. As with the NuGet CLI, the dotnet CLI does not interact with Visual Studio projects.|
|[Package Manager Console](/package-manager-console)    |Visual Studio on Windows    |Consumption    |Provides PowerShell commands for installing and managing packages in Visual Studio projects.|
|[Package Manager UI](/package-manager-ui)    |Visual Studio on Windows    |Consumption    |Provides an easy-to-use UI for installing and managing packages in Visual Studio projects.|
|Manage NuGet UI    |Visual Studio for Mac    |Consumption    |Provide an easy-to-use UI for installing and managing packages in Visual Studio for Mac projects.|
|MSBuild    |Windows    |Creation, Consumption    |Provides the ability to create packages and restore packages used in a project directly through the MSBuild toolchain.|
