---
Permalink: articles/carloscds-ef6-ngen-to-improve-load-time
Title: Improve Entity Framework 6 Performance by Pre-compiling the DLL
MetaDescription: Learn how to use Ngen.exe command to pre-compile the EntityFramework.dll in Entity Framework 6 to improve performance and reduce application load time.
LastMod: 2023-02-24
tags: carloscds article ngen
OriginalLink: https://carloscds.net/2013/11/melhorando-o-tempo-de-carga-do-entity-framework-com-ngen/
CreatedDate: 2013-11-03
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# Improve Entity Framework 6 Performance with Ngen.exe

Beginning with Entity Framework 6, all EF components are contained within the `EntityFramework.dll` and are no longer divided between various .NET Framework components. Therefore, the EF DLL is not optimized for the JIT (Just in Time) of the computer where it is being run, just as .NET is.

We can improve this by running the [Ngen.exe](https://docs.microsoft.com/en-us/dotnet/framework/tools/ngen-exe-native-image-generator) command, which pre-compiles the DLL and eliminates this process while loading the DLL. This way, the charging time gets shorter, and we can save a few seconds on our application load.

To do this, open a Visual Studio Studio command prompt, it is within the Visual Studio installation options, with a name similar to this: "Developer Command Prompt for VS 20xx". In my case, I'm using VS 2013, so the prompt is "Developer Command Prompt for VS2013".

Open this prompt as an administrator, go to your application directory and run the NGen command, as below:

```prompt
C: My Project> ngen install EntityFramework.dll
```

After that you will see a screen that looks like this:

<img src="https://www.carloscds.net/wp-content/uploads/2013/11/image_thumb2.png" width="597" height="150" alt="NGen command">