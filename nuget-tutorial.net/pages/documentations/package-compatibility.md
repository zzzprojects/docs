---
PermaID: 100008
Name: Package Compatibility
---

# Package Compatibility

A compatible package means that it contains assemblies built for at least one target .NET framework that's compatible with the consuming project's target framework. 

 - Developers can create packages that are specific to one framework, as with UWP controls, or they can support a wider range of targets. 
 - To maximize a package's compatibility, developers target .NET Standard, which all .NET and .NET Core projects can consume. 
 - It is the most efficient means for both creators and consumers, as a single package (usually containing a single assembly) works for all-consuming projects.

### Multi-targeting

Package developers who require APIs outside of .NET Standard, on the other hand, create separate assemblies for the different target frameworks they want to support and include all of those assemblies in the same package which is called **multi-targeting**. 

 - When a consumer installs such a package, NuGet extracts only those assemblies that are needed by the project. 
 - It minimizes the package's footprint in the final application and assemblies produced by that project. 
 - A multi-targeting package is, of course, more difficult for its creator to maintain.
