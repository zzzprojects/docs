# Add an Alert

Display an alert or action sheet when your app requires additional information or acknowledgment from the user. Alerts and action sheets interrupt your app's normal flow to display a message to the user.

In our task list app, we need a way to reset the list without using Xcode to reinstall and relaunch the app. From Object Library add a new Bar Button Item to the toolbar at the bottom. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/alerts1.png">

In the Attributes Inspector, change the title to “Reset” it. Now we need an action for this button, so control-drag from Document Outline to the View Controller class.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/alerts2.png">

Make sure that it is an Action and call it "resetList" and then click on Connect button. Now in this method, we will mark the completed property to false, but before doing that, we will also show a confirmation message to a user as shown in the following code.

```csharp
@IBAction func resetList(_ sender: Any) {
    
    let confirm =  UIAlertController(title: "Are You Sure?", message: "Really reset the list?", preferredStyle: .alert)
    
    let yesAction =  UIAlertAction(title: "Yes", style: .destructive) {
        action in
        for i in 0..<self.dailyTasks.count {
            self.dailyTasks[i].completed = false
        }
        
        for i in 0..<self.weeklyTasks.count {
            self.weeklyTasks[i].completed = false
        }
        
        for i in 0..<self.monthlyTasks.count {
            self.monthlyTasks[i].completed = false
        }
        
        self.taskTableView.reloadData()
    }
    
    let noAction = UIAlertAction(title: "No", style: .cancel) {
        action in
        print("That was a close one!")
    }
    
    // add actions to alert controller
    confirm.addAction(yesAction)
    confirm.addAction(noAction)
    
    // show it
    present(confirm, animated: true, completion: nil) 
}
```

This is a custom method, and we are calling it directly from the toolbar button, it is not one of the table view delegate methods, so inside this we have no reference to the table view itself. There is no way to tell the table view to reload and redraw itself, so we need an outlet for a table view. Control-drag from table view to the view controller class.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/alerts3.png">

Make sure to select Outlet from the Connection and call it taskTableView. Now you can use this outlet to reload the table view as shown in the "resetList" method with the following call.

```csharp
self.taskTableView.reloadData()
```

Let's run your application and mark some task as completed.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/alerts4.png">

Click the Reset button, and you will see the following alert.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/alerts5.png">

Now if you click No, then nothing will happen, but if you click on Yes then the completed mark will be removed.
 
<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/alerts6.png">
