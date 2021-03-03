# How to Configure Table View

The table view is the most common way to show a structured, scrollable list of items and a core competency for an iOS developer. When you see an iOS application showing a scrolling list of items, it is usually a Table View. 

In our FirstApp, we have added separate labels and set their explicit values directly in the storyboard. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/configure-tabe-view1.png" alt="Configure tabe-view1">

 - If we had wanted these to be all dynamic content that we could change from Swift by connecting each label as a separate outlet.
 - But it can be a tedious task if we had dozens, hundreds, or perhaps even an unpredictable amount of items.
 - The Table View is perhaps the classic, most fundamental iOS data-driven control, and you cannot avoid it. 
 - We are talking about a data-driven control, but when you hear the word table, don't think databases and iOS table view has nothing to do with database tables. 

## Configuration

The idea of a table is defined by a certain number of rows and columns. In iOS, we don't have to worry about columns, because here a table view is only one column wide. 

 - It is always one column with multiple rows. 
 - Each row in the table view contains one cell, and the cell is just a container to hold the contents of that row.
 - The contents can be whatever you want, maybe that cell contains a single label or more and an image

### Sections and Headings

 - You can break the rows into different sections if you need to make some kind of visual distinction between one set of rows and another set of rows.  
 - The easiest way to understand this is just to look at the iOS Settings application, where you'll see groupings of rows with gray gaps between them. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/configure-tabe-view2.png" alt="configure tabe-view2">

The gray gaps distinguish sections in a table view, sometimes it is just a gap between the sections, but you can also provide headings or footers to explain what the sections are. 

### Cell Formatting

It is common for your rows to all use the same format, but you can also have different kinds of cell layout and styles on your table.

### Provide Data

The first question is, how to provide data to a table view? When you want to add an iOS table view, it will need at least two things

 - Number of rows
 - The content of the cell for each row. 

When configuring a table view, it expects to be explicitly connected to an object in our application that will act as its data source.
