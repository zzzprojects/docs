---
PermaID: 100010
Name: Package Manager UI
---

# Package Manager UI

The NuGet Package Manager UI in Visual Studio allows you to easily install, uninstall, and update NuGet packages in projects and solutions. The Package Manager UI is not included with Visual Studio Code.

You can open the NuGet Package Manager UI by right-clicking on either **References** or a project and select **Manage NuGet Packages...**.

<img src="https://raw.githubusercontent.com/zzzprojects/nuget-tutorial/master/docs/images/package-manager-ui.png" alt="package manager UI">

## Install Package

The Browse tab displays packages by popularity from the currently selected source. 

 - You can search for a specific package using the search box on the upper left. 
 - Select a package from the list to display its information, which also enables the Install button along with a version-selection drop-down.

<img src="https://raw.githubusercontent.com/zzzprojects/nuget-tutorial/master/docs/images/package-manager-ui1.png" alt="package manager uil">

 - Select the desired version from the drop-down and select Install. 
 - Visual Studio installs the package and its dependencies into the project. 
 - When installation is complete, the added packages appear on the Installed tab. 
 - Packages are also listed in the References node of Solution Explorer.

## Uninstall Package

You can uninstall a package using Package Manager UI by selecting the package and click on Uninstall button.

<img src="https://raw.githubusercontent.com/zzzprojects/nuget-tutorial/master/docs/images/package-manager-ui2.png" alt="package manager ui2">

The **Include prerelease**, and **Package source** controls do not affect when uninstalling packages.

## Update Package

You can also update a package or packages using Package Manager UI. 

 - Select the Updates tab to see packages that have available updates from the selected package sources. 
 - Select Include prerelease to include prerelease packages in the update list.

<img src="https://raw.githubusercontent.com/zzzprojects/nuget-tutorial/master/docs/images/package-manager-ui3.png" alt="package manager ui3">

Select the package to update, select the desired version from the drop-down on the right, and select Update.