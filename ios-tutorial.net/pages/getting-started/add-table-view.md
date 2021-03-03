# Add iOS Table View and Data Source

In Xcode, there is a project template option called the Master-Detail App, which already includes a configured table view, but it has a lot of other functionality as well. Here we will focus only on Table View, so let's create a new blank Single View App and call it **TableViewDemo**

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view1.png" alt="Add IOS table view-1">

Once the project is created, go into Main.storyboard, and from the Object library find and drag a Table View. 
 
<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view2.png" alt="Add IOS table view-2">

Now resize it to take up the full contents of the available space.
 
<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view3.png" alt="Add IOS table view-3">

Right click on the table and you will see a popup. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view4.png" alt="Add IOS table view-4">

Click the circle beside dataSource and drag it over to the ViewController in Document Outline.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view5.png" alt="Add IOS table view-5">

You should now see a filled circle, dataSource is now connected to ViewController.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view6.png" alt="Add IOS table view-6">

The Table View will now expect to find its necessary methods in our ViewController class file, so let's go and write them. 

Open the ViewController.swift file, and in the class declaration, you can see that the class ViewController inherits from UIViewController class. After that add ", UITableViewDataSource" as shown below.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view7.png" alt="Add IOS table view-7">

This is not a class name; it is a protocol name, a protocol being a formal list of methods and properties that we can volunteer to provide. 

 - You will also see an error Type 'ViewController' does not conform to protocol 'UITableViewDataSource'
 - Because we need to provide at least a couple of required methods, if you want to look deeper at this protocol, go to the Protocol Reference, and you will see that there are eleven different methods that you can use when working with a table view. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view8.png" alt="Add IOS table view-8">

Now many of these methods are optional, in fact, only two of them are marked as required. The two methods marked as required are the tableView numberOfRowsInSection, and tableView cellForRowAt IndexPath.

So let's add the required methods by clicking the red circle in pop-up error and click Fix button.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view9.png" alt="Add IOS table view-9">

Xcode now provides the stubs for our two required methods, numberOfRowsInSection, and cellForRowAt indexPath. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view10.png" alt="Add IOS table view-10">

NumberOfRowsInSection will be called, and we just need to return an integer value of how many many rows we want in a section, so let's return 20. 

```csharp
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return 20
}    
```

In the next method, we need is cellForRowAt indexPath, providing the content of the cell for each row in the table view. 

 - This method will be automatically called once for each row
 - Each time the method will be passed an indexPath parameter containing two integers, section number, and row number.
 - We can ignore the section number at the moment because we don't have sections yet
 - So, each time this method is called, we will need to return a cell object. 
 - Now a cell can be configured in a lot of different ways, but the default cell object contains a property called textLabel, so set its text property.

```csharp
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = UITableViewCell()
    cell.textLabel?.text = "This is the cell for \(indexPath.row)"
    return cell
}
```

Let's run your application. 
 
<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/add-ios-table-view11.png" alt="Add IOS table view-11">
