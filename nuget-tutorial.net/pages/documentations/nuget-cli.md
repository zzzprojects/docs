---
PermaID: 100005
Name: NuGet CLI
---

# NuGet CLI

The NuGet (`nuget.exe`) CLI, provides the full extent of NuGet functionality to install, create, publish, and manage packages without making any changes to project files.

## Download & Installation

You can download the latest version from [nuget.org/downloads](https://www.nuget.org/downloads). 

 - The latest version is always recommended, and 4.1.0+ is required to publish packages to nuget.org.
 - The file is not an installer, and it is the `nuget.exe` file directly. 
 - You won't see anything if you run it directly from the browser.
 - Add the folder where you placed `nuget.exe` to your PATH environment variable to use the CLI tool from anywhere.

## Commands and Applicability

Available commands and applicability to package creation, package consumption, and publishing a package to a host:

### Common Commands

|Commands    |Applicable Roles    |NuGet Version    |Description    |
|:--------- |:----------------- |:------------- |:------------- |
|pack        |Creation            |2.7+            |Creates a NuGet package from a .nuspec or project file. When running on Mono, creating a package from a project file is not supported.|
|push        |Publishing            |All            |Publishes a package to a package source. |
|config        |All                |All            |Gets or sets NuGet configuration values. |
|help or ?    |All                |All            |Displays help information or help for a command. |
|locals        |Consumption        |3.3+            |Lists locations of the global-packages, http-cache, and temp folders and clears the contents of those folders.|
|restore    |Consumption        |2.7+            |Restores all packages referenced by the package management format in use. When running on Mono, restoring packages using the PackageReference format is not supported.|
|setapikey    |Publishing, Consumption    |All    |Saves an API key for a given package source when that package source requires a key for access.|
|spec        |Creation            |All            |Generates a .nuspec file, using tokens if generating the file from a Visual Studio project. |

### Secondary Commands

|Commands    |Applicable Roles    |NuGet Version    |Description    |
|:--------- |:----------------- |:------------- |:------------- |
|add        |Publishing            |3.3+            |Adds a package to a non-HTTP package source using hierarchical layout. For HTTP sources, use push.|
|delete        |Publishing            |All            |Removes or unlists a package from a package source.|
|init        |Creation            |3.3+            |Adds packages from a folder to a package source using hierarchical layout.|
|install    |Consumption        |All            |Installs a package into the current project but does not modify projects or reference files.|
|list        |Consumption, perhaps Publishing    |All    |Displays packages from a given source.|
|mirror        |Publishing            |Deprecated in 3.2+    |Mirrors a package and its dependencies from a source to a target repository.|
|sources    |Consumption, Publishing    |All    |Manages package sources in configuration files.|
|update        |Consumption        |All            |Updates a project's packages to the latest available versions. Not supported when running on Mono.|

## Examples

The following are the most commonly used commands with examples.

### Add

Adds a specified package to a non-HTTP package source (a folder or UNC path) in a hierarchical layout, wherein folders are created for the package ID and version number.

```csharp
nuget add foo.nupkg -Source c:\bar\
nuget add foo.nupkg -Source \\bar\packages\
```

### Delete

Deletes or unlists a package from a package source. 

```csharp
nuget delete MyPackage 1.0
nuget delete MyPackage 1.0 -Source http://package.contoso.com/source -apikey A1B2C3
```

### Config

Gets or sets NuGet configuration values. 

```csharp
nuget config -Set repositoryPath=c:\packages -configfile c:\my.config
nuget config -Set repositoryPath=
nuget config -Set repositoryPath=%PACKAGE_REPO% -configfile %ProgramData%\NuGet\NuGetDefaults.Config
nuget config -Set HTTP_PROXY=http://127.0.0.1 -set HTTP_PROXY.USER=domain\user
```

### Install

Downloads and installs a package into a project, defaulting to the current folder, using specified package sources.

```csharp
nuget install elmah
nuget install packages.config
nuget install ninject -OutputDirectory c:\proj
```

### Pack

Creates a NuGet package based on the specified .nuspec or project file.

```csharp
nuget pack
nuget pack foo.nuspec
nuget pack foo.csproj
nuget pack foo.csproj -Properties Configuration=Release
nuget pack foo.csproj -Build -Symbols -Properties owners=janedoe,xiaop;version="1.0.5"
```

### Push

Pushes a package to a package source and publishes it.

```csharp
nuget push foo.nupkg
nuget push foo.symbols.nupkg
nuget push foo.nupkg -Timeout 360
nuget push *.nupkg
```

### Restore

Downloads and installs any packages missing from the packages folder. 

```csharp
# Restore packages for a solution file
nuget restore a.sln

# Restore packages for a solution file, using MSBuild version 14.0 to load the solution and its project(s)
nuget restore a.sln -MSBuildVersion 14

# Restore packages for a project's packages.config file, with the packages folder at the parent
nuget restore proj1\packages.config -PackagesDirectory ..\packages

# Restore packages for the solution in the current folder, specifying package sources
nuget restore -source "https://api.nuget.org/v3/index.json;https://www.myget.org/F/nuget"
```

### Update

Updates all packages in a project (using packages.config) to their latest available versions. It is recommended to run `restore` before running the `update`. 

```csharp
nuget update

# update packages installed in solution.sln, using MSBuild version 14.0 to load the solution and its project(s).
nuget update solution.sln -MSBuildVersion 14

nuget update -safe
```