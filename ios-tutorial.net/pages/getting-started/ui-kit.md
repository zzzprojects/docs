# UIKit

## Introduction

The UIKit framework provides the required infrastructure for your iOS apps. It provides the core objects that you need to build apps for iOS and tvOS. You use these objects to display your content onscreen, to interact with that content, and to manage interactions with the system. 

 - In iOS, user interface classes are not just called Button, Label TextField, and slider, but rather UIButton, UILabel, UISlider, UITextField.
 - You will also see things like UIColor, UITouch, UIEvent, and UIPrinter, etc. 
 - All of this provided functionality which is bundled together into a single framework that Apple calls UIKit.

If you looked at our example, and open the ViewController.swift file. 

```csharp

//
//  ViewController.swift
//  FirstApp
//

import UIKit

class ViewController: UIViewController {

    @IBAction func changeBackground(_ sender: Any) {
        view.backgroundColor = UIColor.lightGray
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

We have added one behavior with one line of code to change the background color when a button is clicked.

```csharp

@IBAction func changeBackground(_ sender: Any) {
    view.backgroundColor = UIColor.lightGray
}

```

This line of code links our Swift file to that UIKit framework and will allow us access to everything inside it.

 - You will see `import UIKit` at the top of the swift file mostly. 
 - It links all the stuff Apple wrote to support making a typical iOS application, and not just the user interface behaviors, but all the infrastructure content as well.
 - When you build more complex and specialized iOS apps, you might need to import other frameworks as well, but you will always need UIKit.
