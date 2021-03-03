# Sections & Headings

A table view is made up of zero or more sections, each with its own rows. Sections are identified by their index number within the table view, and rows are identified by their index number within a section. 

 - A section header can optionally precede any section, and optionally be followed by a section footer.
 - Table views can have one of two styles, UITableViewStylePlain and TableViewStyleGrouped. 
 - When you create a UITableView instance, you must specify a table style, and this style cannot be changed. 
 - In the plain style, section headers and footers float above the content if the part of a complete section is visible. 

Let's go to our FirstApp where we have created TaskList which contains Daily Tasks, Weekly Tasks, and Monthly Tasks. 

 - Now we want to move that task list to a table view, and split it into three different sections and add headers for each section.
 - The storyboard has absolutely nothing to do; we need to create three separate arrays of strings for our tasksViewController.swift file.

```csharp
let dailyTasks = ["daily task 1",
                  "daily task 2",
                  "daily task 3",
                  "daily task 4",
                  "daily task 5"];

let weeklyTasks = ["weekly task 1",
                  "weekly task 2",
                  "weekly task 3"];

let monthlyTasks = ["monthly task 1",
                   "monthly task 2",
                   "monthly task 3",
                   "monthly task 4"];
```

Now we need to implement the following methods. 

```csharp
func numberOfSections(in tableView: UITableView) -> Int {
    //code here
}

func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    //code here
}

func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    //code here
}
```

You can see that this time we have one extra method to implement .i.e `numberOfSections(in tableView: UITableView) -> Int`. We have three different sections, one for daily tasks, one for weekly tasks, one for monthly tasks. So here we have just to return the number 3.

```csharp
func numberOfSections(in tableView: UITableView) -> Int {
    return 3
}
```

Next, we need to implement the `tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int` method. This time we have to add a little bit of logic so that we can return a different number for each section, based on the size of the array.

```csharp
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    switch section {
    case 0:
        return dailyTasks.count
    case 1:
        return weeklyTasks.count
    case 2:
        return monthlyTasks.count
    default:
        return 0
    }
}
```

Moving on to the second required method ` tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell` to return the cell for each row. So, once again, this method is called automatically, and it will ask for a UITableViewCell to be returned for each row of each section.

```csharp
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = UITableViewCell()
    switch indexPath.section {
    case 0:
        cell.textLabel?.text = dailyTasks[indexPath.row]
    case 1:
        cell.textLabel?.text = weeklyTasks[indexPath.row]
    case 2:
        cell.textLabel?.text = monthlyTasks[indexPath.row]
    default:
        cell.textLabel?.text = "Invalid section"
    }
    return cell
}
```

The ViewController.swift file looks like this.

```csharp
//
//  ViewController.swift
//  TableViewDemo
//
//  Copyright © 2018 ZZZ Projects. All rights reserved.
//
import UIKit
class ViewController: UIViewController, UITableViewDataSource {
    func numberOfSections(in tableView: UITableView) -> Int {
        return 3
    }
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        switch section {
        case 0:
            return dailyTasks.count
        case 1:
            return weeklyTasks.count
        case 2:
            return monthlyTasks.count
        default:
            return 0
        }
    }
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = UITableViewCell()
        switch indexPath.section {
        case 0:
            cell.textLabel?.text = dailyTasks[indexPath.row]
        case 1:
            cell.textLabel?.text = weeklyTasks[indexPath.row]
        case 2:
            cell.textLabel?.text = monthlyTasks[indexPath.row]
        default:
            cell.textLabel?.text = "Invalid section"
        }
        return cell
    }
    let dailyTasks = ["daily task 1",
                      "daily task 2",
                      "daily task 3",
                      "daily task 4",
                      "daily task 5"]
    
    let weeklyTasks = ["weekly task 1",
                      "weekly task 2",
                      "weekly task 3"]

    let monthlyTasks = ["monthly task 1",
                      "monthly task 2",
                      "monthly task 3",
                      "monthly task 4"]

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
}
```

Let's run the application.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/section-heading1.png" alt="Section heading-1">

You can see the right data, but it still looks like one section, they are all just one after the other. Now we want to have headings for each section. So let's go into Xcode and add a new method ` tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String?` and  return a string based on the section number. 

```csharp
func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
      switch section {
    case 0:
        return "Daily Tasks"
    case 1:
        return "Weekly Tasks"
    case 2:
        return "Monthly Tasks"
    default:
        return nil
    }
}
```

Let's run your application again

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/section-heading2.png" alt="Section heading-2">

You can see headings, but it is useful for some visual separation between sections. So go back into Xcode and open the Main.storyboard. Select Table View and open up the Attribute Inspector in the upper section in the right panel and change the style from Plain to Grouped. 

Run your application and now you will see more separation between our different sections and the rows in those sections. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/section-heading3.png" alt="Section heading-3">