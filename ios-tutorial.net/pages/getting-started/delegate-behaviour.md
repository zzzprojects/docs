# Table View Delegate Behavior

In our demo application, we have provided an implementation for some methods to display data in the table view, mentioned the number of sections and rows, and specify headers and footer information for each section.

 - These methods describe the contents of the table view, and they don't say anything about the behavior of it.
 - For example, what happens when a row is tapped, and can we swipe to delete rows or rearrange rows
 - Table View supports these features and includes all the visual aspects of swiping to delete or selecting or rearranging, but by default, that functionality is turned off.
 - if we want to add this functionality, we will need to include some additional methods to tell the table view we are providing that behavior
 - But these methods are not in the data source protocol; they are in the UITableViewDelegate protocol for the table view.

## Delegate protocol

The data source defines methods to handle the data, and the delegate protocol defines methods to handle custom behavior and interactivity, and custom appearance.

 - The delegate of a UITableView object must adopt the UITableViewDelegate protocol. 
 - Optional methods of the protocol allow the delegate to manage selections, configure section headings and footers, help to delete and reorder cells and perform other actions.

So let's go to the **TableViewDemo** project again open Main.storyboard. Right-click on the Table View and connect the circle beside the delegate to the View Controller.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/table-view-delegate1.png">

Now let's go the ViewController.swift file and add a comma followed by UITableViewDelegate.

```csharp
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
}
```

In Swift, you can only inherit from a single class, we are inheriting from the UIViewController class, but there's no limit to the number of protocols you can choose to support. There are multiple optional methods available like;

 - To change the visual appearance of the Table View
 - To provide specific heights or indentation levels for rows, 
 - Managing that row being selected
 - To modify the header and the footer 
 - To provide custom views and custom visuals, methods for editing, re-ordering, or even for managing the focus or handling swipe actions.

So let's add one of the simpler options is what will happen when a row is selected, and that method is defined in the UITableViewDelegate protocol.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/table-view-delegate2.png">

If you provide that method, it will automatically be called, so let's add the implementation for this method ViewController class.

```csharp
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    print("You select row \(indexPath.row) in section \(indexPath.section) ")
}
```

This method will automatically be called whenever somebody touches a row, and it will also give the section number and the row number. We have added a simple print statement to show that selected row and selected section. Let's run the application. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/table-view-delegate3.png">

If you touch one of these rows to select you should see a message in the console view of Xcode. So we could use this method to prepare to move to another screen and use that selected row to make sure we've passed over any information that we need on that next screen.
