---
Permalink: articles/carloscds-ef6-power-tools
Title: How to Install Entity Framework Power Tools in Visual Studio 2015
MetaDescription: Learn how to install Entity Framework Power Tools in Visual Studio 2015 with this step-by-step guide. Don't miss out on this useful tool for EF Code First.
LastMod: 2023-02-24
tags: carloscds article power-tools
OriginalLink: https://carloscds.net/2015/12/entity-framework-powertool-no-visual-studio-2015/
CreatedDate: 2015-12-21
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# How to Install Entity Framework Power Tools in Visual Studio 2015

If you use EF Code First a lot like me and are already in Visual Studio 2015, you must have missed EF Power Tools. Unfortunately, it has not yet been ported to this version of Visual Studio.

But calm down, there is a way to install the current version in VS 2015. To do this, first, download the [Visual Studio Galery](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EntityFrameworkPowerToolsBeta4) component. See in the image below that it does not support VS 2015:

<img src="https://carloscds.net/wp-content/uploads/2015/12/image_thumb.png" width="468" height="219" alt="Entity Framework Power Tools Beta 4">

Now comes the trick ...

I don't know if you know, but the VSIX file is actually a ZIP file, so let's rename the downloaded file to `.ZIP` and then we'll export the content in the same directory, looking like this:

<img src="https://carloscds.net/wp-content/uploads/2015/12/image_thumb1.png" width="494" height="188" alt="extension.vsixmanifest">

Now we are going to open the file `extension.vsixmanifest`, because that is where the supported versions of Visual Studio are located. With the file open we will add the lines, just below the version 12.0 block:

```xml
<VisualStudio Version = "14.0">
     <Edition> Pro </Edition>
</VisualStudio>
```

Change the file and save it, it will look like this:

<img src="https://carloscds.net/wp-content/uploads/2015/12/image_thumb2.png" width="482" height="305" alt="Visual Studio powerr tools">

Now we need to compress everything again. Let's do this and naming it `EFPowerToolsVS2015.zip`. Don't forget to delete the original file first (which we renamed to `.ZIP`). We will now have the following list of files:

<img src="https://carloscds.net/wp-content/uploads/2015/12/image_thumb3.png" width="215" height="240" alt="EFPowerToolsVS2015">

To finish, rename the `.ZIP` file to `.VSIX` and click to install!

There, now you have the Entity Framework Power Tools working on your Visual Studio 2015!

If you don't want to perform the steps above, click [here](https://github.com/carloscds/Palestras/blob/master/Tools/EFPowerToolsVS2015.zip) and download the VSIX file ready!