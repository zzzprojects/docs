---
PermaID: 100002
Name: Dotnet CLI
---

# Dotnet CLI

The .NET Core 2.0 CLI (`dotnet.exe`), works on all platforms (Windows, Mac, and Linux) and provides core NuGet features such as installing, restoring, and publishing packages. 

 - The dotnet provides direct integration with .NET Core project files (such as `.csproj`), which is helpful in most scenarios. 
 - It is also built directly for each platform and does not require you to install Mono.

## Installation

 - On developer computers, install the [.NET Core SDK](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial).
 - For build servers, follow the instructions on [Using .NET Core SDK and tools in Continuous Integration](https://docs.microsoft.com/en-us/dotnet/core/tools/using-ci-with-cli).

## Commands

The dotnet CLI provides the following essential `nuget.exe` commands.

### Add

Adds a package reference to a project file.

#### Examples

Add Newtonsoft.Json NuGet package to a project.

```csharp
dotnet add package Newtonsoft.Json
```

Add a specific version of a package to a project.


```csharp
dotnet add ToDo.csproj package Microsoft.Azure.DocumentDB.Core -v 1.0.0
```

Add a package using a specific NuGet source.

```csharp
dotnet add package Microsoft.AspNetCore.StaticFiles -s https://dotnet.myget.org/F/dotnet-core/api/v3/index.json
```

### Remove

Removes package reference from a project file. 

#### Examples

Removes `Newtonsoft.Json` NuGet package from a project in the current directory.

```csharp
dotnet remove package Newtonsoft.Json
```

### Restore

Restores the dependencies and tools of a project. 

#### Examples

Restore dependencies and tools for the project in the current directory.

```csharp
dotnet restore
```

Restore dependencies and tools for the app1 project found in the given path.

```csharp
dotnet restore ~/projects/app1/app1.csproj
```

Restore the dependencies and tools for the project in the current directory using the file path provided as the source.

```csharp
dotnet restore -s c:\packages\mypackages
```

Restore the dependencies and tools for the project in the current directory using the two file paths provided as sources.

```csharp
dotnet restore -s c:\packages\mypackages -s c:\packages\myotherpackages
```

Restore dependencies and tools for the project in the current directory and shows only minimal output.

```csharp
dotnet restore --verbosity minimal
```

### Locals

Clears or lists local NuGet resources.

#### Examples

Displays the paths of all the local cache directories (http-cache directory, global-packages cache directory, and temporary cache directory).

```csharp
dotnet nuget locals –l all
```

Displays the path for the local http-cache directory.

```csharp
dotnet nuget locals --list http-cache
```

Clears all files from all local cache directories (http-cache directory, global-packages cache directory, and temporary cache directory):

```csharp
dotnet nuget locals --clear all
```

Clears all files in the local global-packages cache directory.

```csharp
dotnet nuget locals -c global-packages
```

Clears all files in the local temporary cache directory.

```csharp
dotnet nuget locals -c temp
```

### Pack

Packs the code into a NuGet package.

#### Examples

Pack the project in the current directory.

```csharp
dotnet pack
```

Pack the app1 project.

```csharp
dotnet pack ~/projects/app1/project.csproj
```

Pack the project in the current directory and place the resulting packages into the nupkgs folder.

```csharp
dotnet pack --output nupkgs
```

### Push

Pushes a package to the server and publishes it.

#### Examples

Pushes foo.nupkg to the default push source, specifying an API key:

```csharp
dotnet nuget push foo.nupkg -k 4003d786-cc37-4004-bfdf-c4f3e8ef9b3a
```

Push foo.nupkg to the custom push source https://customsource, specifying an API key:

```csharp
dotnet nuget push foo.nupkg -k 4003d786-cc37-4004-bfdf-c4f3e8ef9b3a -s https://customsource/
```

Pushes foo.nupkg to the default push source:

```csharp
dotnet nuget push foo.nupkg
```

Pushes foo.symbols.nupkg to the default symbols source:

```csharp
dotnet nuget push foo.symbols.nupkg
```

Pushes foo.nupkg to the default push source, specifying a 360-second timeout:

```csharp
dotnet nuget push foo.nupkg --timeout 360
```

Pushes all .nupkg files in the current directory to the default push source:

```csharp
dotnet nuget push *.nupkg
```

### Delete

Deletes or unlists a package from the server.

#### Examples

Deletes version 1.0 of package Microsoft.AspNetCore.Mvc:

```csharp
dotnet nuget delete Microsoft.AspNetCore.Mvc 1.0
```

Deletes version 1.0 of package Microsoft.AspNetCore.Mvc, not prompting user for credentials or other input:

```csharp
dotnet nuget delete Microsoft.AspNetCore.Mvc 1.0 --non-interactive
```
