# Access Subviews

## How to Access Subviews

Subview is childview which is added on any view. In iOS, UIView class is an object that manages the content for a rectangular area on the screen. UIView class contains the property ` var subviews: [UIView] `

- Each view can contain multiple subviews of the same class UIView or its descendants.
- You can use the ` subviews ` property to retrieve the subviews associated with your custom view hierarchies. 
- The order of the subviews in the array reflects their visible order on the screen, with the view at index 0 being the back-most view.

To access the subviews, we need to change the behaviour a little bit. In our FirstApp, when  you click on a **Change Background** button, it only change the background colour of our top-level view to light gray. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/access-subviews1.png">
 
 - Now let's say we also want to change the text colour of all the label to a lighter colour to add some contrast on the dark background.
 - If you are from programming backgrounds, you might think that when you drag and drop a label or a button or anything else onto a storyboard in Xcode. 
 - Then it will probably generating some unique identifier for each control.
 - And then you can just use those unique identifier in your Swift code to change the text of button or change the colour label.
 - That is not the case in iOS, here we have only the top-level container view object which was already configured in the standard Xcode iOS project template with the name view.

To retrieve all the labels and change the text colour, then we will need to use the `subViews` property of view, and view is an instance of UIView. 

```csharp
@IBAction func changeBackground(_ sender: Any) {
    view.backgroundColor = UIColor.lightGray
    
    let subViews = view.subviews
    
    for eachView in subViews {
        if eachView is UILabel {
            let currentLabel = eachView as! UILabel
            currentLabel.textColor = UIColor.green
        }
    }
}
```
In this code the first line is 

```csharp
let subViews = view.subviews
```

 - It will retrieve an array of all the subviews from a top-level view. 
 - Here we want to change the text colour of labels, so we need an array of UILabel objects.
 - But this can't be an array of UILabel objects, because there is also a UIButton on this storyboard.
 - So each item in the array will be typed not as a UILabel or a UIButton, but just as a general UIView object all UI controls inherit this UIView class. 

```csharp
for eachView in subViews {
    if eachView is UILabel {
        let currentLabel = eachView as! UILabel
        currentLabel.textColor = UIColor.white
    }
}
```

 - Then we are iterating over each item in this array and then check if it is really is a UILabel, using the `is` keyword.
 - If it is a UILabel, then we need to downcast it to more specific UILabel from the a UIView and store the reference in `currentLabel`.

Now we can change the text colour of a label using the `textColor` property

```csharp
currentLabel.textColor = UIColor.white`
```

Let's run your application and click on the **Change Background** button, and you will see that with background the colour of label text also changed when the button is clicked.
 
<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/access-subviews2.png">
