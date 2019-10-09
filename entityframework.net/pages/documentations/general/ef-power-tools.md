---
PermaID: 1000102
Name: EF Power Tools
---

# Power Tools

[Entity Framework 6 Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) are very useful design-time utilities which can be used in Visual Studio.

You can download and install it from [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).

When right-clicking on a file containing a derived DbContext class, the following context menu functions are available:

 - **View Entity Data Model (Read-only):** It displays a read-only view of the Code-First model in the Entity Model Designer.
 - **View Entity Data Model XML:** It displays the EDMX XML representing the underlying Code-First model.
 - **View Entity Data Model DDL SQL:** It displays the DDL SQL corresponding to the SSDL in the underlying EDM Model.
 - **Generate Views:** It generates pre-compiled views used by the EF runtime to improve start-up performance. Adds the generated views file to the containing project.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/power-tools1.png">

When right-clicking on an Entity Data Model .edmx file, you will see only **Generate Views** context.

 - Generate Views

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/power-tools2.png">