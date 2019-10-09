---
PermaID: 100011
Name: Tracking References
---

# Tracking References

Projects can easily move between developer computers, source control repositories, build servers, etc., it's highly impractical to keep the binary assemblies of NuGet packages directly bound to a project. 

 - It would make each copy of the project unnecessarily bloated. 
 - It would also make it very difficult to update package binaries to newer versions as updates would have to be applied across all copies of the project.

NuGet maintains a simple reference list of the packages upon which a project depends, including both top-level and down-level dependencies. 

 - when you install a package from some host into a project, NuGet records the package identifier and version number in the reference list. 
 - NuGet then provides a means to restore all referenced packages upon request.