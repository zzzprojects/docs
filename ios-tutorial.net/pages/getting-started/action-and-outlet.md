# Action & Outlet

UIViewController can communicate with its UIView directly using Action and Outlet connections.

 - When a View Controller needs to update the label text, it will make that outlet connection. 
 - When a button is tapped, it passes the message to the View Controller by using the action connection. 
 - It means the outlet created in the View Controller is declared as a property, and action is created by declaring a method. 

Let's have a look the Action and Outlet in working by creating a new Single View App and call **ActionAndOutletDemo**.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/action-outlet1.png">

Once the project is created, add one label and one button as shown below.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/action-outlet2.png">

 - So here we want when you touch the button, it executes some code to change the text of the label. 
 - That means this button needs to be an action to cause something to happen. 
 - And also, the label needs to be an outlet, because we want to change it from our Swift Controller code.

Let's hide the left and right panels, select the storyboard and then switch to the Assistant mode. 

Hold the Control key down, then click on the label and drag over to your ViewController class and release the mouse above any existing methods. Now you will see a small pop up where the Connection is set to an outlet. Let's give it a name myLabel and click Connect button.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/action-outlet3.png">

Next, go to the button in the storyboard, hold the control button again, click the button and drag over below where we have created the basicLabel. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/action-outlet4.png">

This time when the pop-up appears, make sure to change the Connection from an outlet to action. Give it a name changeText. Now our ViewController class have the following additional lines of code. One is the outlet, and the other one is the action.

```csharp
@IBOutlet weak var myLabel: UILabel!

@IBAction func changeText(_ sender: Any) {

}
```

 - You can use the outlet name `myLabel` to change the text of that label. 
 - So the action is implemented as a new method in our Swift, and an outlet is implemented as a new property in our Swift. 
 - Now Xcode has automatically prefixed both of these with a special attribute `@IBOutlet` and `@IBAction`.
 - It is an indicator that these pieces of Swift are not standard methods and properties, but are connected to a user interface file.

Let's add the following line of code to change the text of a label when the button is tapped.

```csharp
myLabel.text = "New Text"
```

Now the ViewController class will look like this.

```csharp
//
//  ViewController.swift
//  ActionAndOutletDemo
//
//  Copyright © 2018 ZZZ Projects. All rights reserved.
//

import UIKit

class ViewController: UIViewController {


    @IBOutlet weak var myLabel: UILabel!
    
    @IBAction func changeText(_ sender: UIButton) {
        myLabel.text = "New Text"
    }
    
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

Run your application

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/action-outlet5.png">

Now when you click the button, it will change the text of the label.
 
<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/action-outlet6.png">
