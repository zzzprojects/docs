# Add Navigation Controller

When your application has multiple View Controllers, then you will also need to handle the navigation between them. If you at our simple demo application in which we have two view controllers and a button that performs a segue from the first to the second. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/navigation-controller1.png">

When you run this application, you will notice that the second screen is going from the bottom and there is no way to transition back to the previous screen. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/navigation-controller2.png">

To add the navigation controller open the storyboard file.
 
<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/navigation-controller3.png">

In the Document Outline, select the first View Controller, go to the Editor menu, and select Embed In > Navigation Controller option.   

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/navigation-controller4.png">

You will now see a third scene, which is a Navigation Controller scene, and Xcode has automatically changed it to the initial view controller.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/navigation-controller5.png">

Let's run your application, and you will see the navigation area at the top. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/navigation-controller6.png">

Now when you click on the button, you will get the animation coming in from the right-hand side, and you will also see the Back button on the second screen which will move back to the first screen. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/navigation-controller7.png">

On both screens, the navigation area at the top is blank, so let's add a title on both screens. Open the first view controller class and update the `viewDidLoad` method by setting the title property of view controller.

```csharp
class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
        
        self.title = "First Scene"
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
} 
```

This method is called automatically when the view controller is loaded. Similarly set the title property of the 2nd view controller.

```csharp
class SecondViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        // Do any additional setup after loading the view.
        
        self.title = "Second Scene"
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    

    /*
    // MARK: - Navigation

    // In a storyboard-based application, you will often want to do a little preparation before navigation
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        // Get the new view controller using segue.destinationViewController.
        // Pass the selected object to the new view controller.
    }
    */
}
```

Let's run your application, and you will now see the title on both scenes.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/navigation-controller8.png">
