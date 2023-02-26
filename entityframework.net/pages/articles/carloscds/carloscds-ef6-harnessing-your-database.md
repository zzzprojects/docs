---
Permalink: articles/carloscds-ef6-harnessing-your-database
Title: Using Entity Framework Power Tools for Code First Development
MetaDescription: Learn how to generate classes and contexts for an existing database using Entity Framework Power Tools, a Visual Studio extension.
LastMod: 2023-02-24
tags: carloscds article power-tools
OriginalLink: https://carloscds.net/2014/04/entityframeworkapoveitando-seu-banco-de-dados-com-powertools/
CreatedDate: 2014-04-21
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# Using Entity Framework Power Tools for Code First Development

When a company decides to adopt an ORM tool (Relational Object Mapping - in our case EntityFramework), a big paradigm break begins, as you will no longer access the database directly (`Select`, `Insert`, `Update`, `Delete`, etc.), and will start to work only with classes and objects. That makes development much easier and more productive since you don't have to worry about the commands that go to the database, as this is ORM's job.

But what about when you already have a database ready? That always generates another discussion. After all, you can build a new database, take advantage of the process and improve everything, or reuse the existing database structure and include only the ORM? You want to hear something very common: you are creating a new functionality or a new project, and you decide to use Entity Framework, but you need to read and write data from the current database. How to make it? Write the POCO classes by hand? Obviously not, because for this, several tools can do this for you, and one that we are going to present today is Entity Framework Power Tools.

To get started you need to download Power Tools using Visual Studio extensions management (Tools / Extensions and Updates), and then search for the Entity Framework Power Tools, as shown in the figure below:

<img src="https://carloscds.net/wp-content/uploads/2014/04/SNAGHTML4333684_thumb.png" width="718" height="412" alt="Database with Power Tools-1">

In my case, it is already installed. But, if yours is not already, click the "Install" button and you're done! Now let's create a project to explore the features.

Let's start by creating a Console project and adding EntityFramework through NuGet:

<img src="https://carloscds.net/wp-content/uploads/2014/04/SNAGHTML434d0ea_thumb.png" width="716" height="410" alt="Database with Power Tools-2">

Now let's add EF:

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb8.png" width="728" height="182" alt="Database with Power Tools-3">

Now that we have Entity Framework, let's check in the menu of our solution a new item called Entity Framework (to see this menu, right-click on the name of your project):

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb9.png" width="744" height="143" alt="Database with Power Tools-4">

See that we have two options:

Reverse Engineer Code First: allows you to read an existing database and generate the classes and the context;

Customize Reverse Engineer Templates: allows changing the generation format of the classes through a T4 file.

If you choose to customize the templates, you will have the following files added to the project, which can be modified according to your needs:

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb10.png" width="442" height="181" alt="Database with Power Tools-5">

Choosing to generate the classes with the option "Reverse Engineer Code First" you will have a screen to put the access data to your database, do this and then click OK to start the generation of the classes:

<img src="https://carloscds.net/wp-content/uploads/2014/04/SNAGHTML43ed31c_thumb.png" width="372" height="574" alt="Database with Power Tools-6">

Now we have the entire database mapped, and in the current version of the Entity Framework, this includes tables and views. See a part of these classes in our solution:

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb11.png" width="312" height="556" alt="Database with Power Tools-7">
Reverse engineering does all the attribute mapping using FluentAPI, and the "Mapping" folder contains these mappings.

Now let's right-click on our context: `NorthwindContext.cs`:

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb12.png" width="633" height="125" alt="Database with Power Tools-8">

See that we now have other options, which are related to the database that was mapped. As we are working with Code First, we do not have a visual diagram of our database. But with the help of Power Tools, by clicking on the first option: View Entity Data Model (Read-only), you can graphically visualize how your database is doing. , see below:

<img src="https://carloscds.net/wp-content/uploads/2014/04/image_thumb13.png" width="605" height="460" alt="Database with Power Tools-9">

See that all relationships have been created and are generated through Fluent API in the mapping file. Note that the menu reads "Read-only" and this means that this diagram is for reading only, so there is no point in writing anything in it that will not be reflected in the entities.

It is also possible to generate an XML file, which is similar to the EDMX file (Model First and Database First). There is also an option to generate a DDL script, which can be executed on the database server to create it. In the case of Entity Framework Code First, there is a migration feature that eliminates this work of creating and updating the database. See this post [here](/articles/carloscds-ef6-code-first-migrations).

Finally, we have a very interesting option: "Generate Views" that generates queries optimized for the database and helps to improve the performance of our application. Remembering that if you choose to use Views, you should generate them whenever you modify your classes.

I hope I have clarified the use of the Entity Framework Power Tools and that this article will be very useful for you!