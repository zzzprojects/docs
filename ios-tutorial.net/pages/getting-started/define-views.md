# Define Views

## How to Define Views

In iOS, a view object means anything that is capable of drawing itself in a rectangular region on an iOS screen. 

 - For example, a label is a view object, and a button is another view object. 
 - When you are looking at the user interface of any random iOS application, that one screen might contain 5 or 10 or maybe more than 20 different view objects. 
 - Some views are large, and some are tiny. Some views might show an image; some might show text, some might show both. 

Let's back in the code for the FirstApp project. In ViewController.swift file we are using a `view` to change the background color. 

 - This view is an object that already exists as part of the storyboard. 
 - It is not referring to any of the view objects we have added, because when you create a new Xcode project, and you will also get that first storyboard. 
 - It included the outline of a single screen, and it is simply called view. 

Now we might describe this as empty, but it is not, because it is a view object configured to draw itself and to fill all available screen size on whatever device it runs on. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/define-views1.png">
 
So in our FirstApp project, all these labels, and a button are all independent view objects. They are currently all nested inside this larger generic view object, and you can see them in Document Outline.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/define-views2.png">

So as this is our top-level view object, all these labels and this button are inside it, so they are all subviews of the top-level view. This idea of views with subviews inside them is useful because in the code we could ask this top-level view to give all of its subviews.