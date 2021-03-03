# Create a Tabbed Application

When you are working on iOS apps with multiple screens or multiple view controllers, another common way of moving from one screen to another screen is to use the Tab Bar Controller. 

 - Conceptually, using the Tab Bar Controller is similar to using a navigation controller. 
 - The Tab Bar Controller will be loaded first because it is the initial view controller.
 - UITabBarController manages a radio-style selection interface, where the selection determines which child view controller to display.
 - The tab bar interface displays tabs at the bottom of the window for selecting different screens.
 - Each tab is associated with a custom view controller class. 

To use the Tab Bar Controller in your application, the simplest way is to create a new project in Xcode using the Tabbed App project type and call it **TabbedAppDemo**. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app1.png" alt="Tabbed Application-1">

It will create a basic app using a tab bar, and you will see three scenes in the storyboard.

 - Tab Bar Controller
 - First Scene
 - Second Scene. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app2.png" alt="Tabbed Application-2">

Let's run your application, and you will see that it start with a tab bar at the bottom with two options, First and Second, and these are two different view controllers. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app3.png" alt="Tabbed Application-3">

These two options at the bottom allow you to switch between them.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app4.png" alt="Tabbed Application-4">

Now we want to add a new section to the tab bar, so go to the storyboard and open the Object library. Drag a View Controller to the blank area of the storyboard. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app5.png" alt="Tabbed Application-5">

Now drag one Label as well and change the text to "Third View", so that we can recognize it when we run our application.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app6.png" alt="Tabbed Application-6">

We have added a new view controller scene onto the storyboard, so it is also a good habit to add a new class for that scene. So go to the File Menu and select **New > File...** menu option.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app7.png" alt="Tabbed Application-7">

Choose Cocoa Touch class and click Next

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app8.png" alt="Tabbed Application-8">

Call it ThirdViewController, and make sure to make it a subclass of the UIViewController. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app9.png" alt="Tabbed Application-9">

Now the third step is to connect your scene to that new class. In the Document Outline, select the newly created View Controller and go to the Identity Inspector.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app10.png" alt="Tabbed Application-10">

It tells you that its class is UIViewController which is the standard default view controller. Click on the drop-down and select the ThirdViewController.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app11.png" alt="Tabbed Application-11">

Right now this ThirdViewController is unreachable in our application, and we need to connect it to the Tab Bar Controller. To do so, hold the control key, click on the Tab Bar Controller, drag over to the third view and you will get a pop-up because Xcode recognizes we want to create another segue, a transition between view controllers.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app12.png" alt="Tabbed Application-12">

Choose the view controllers option under the relationship segue, and you will get a tab bar at the bottom of our new view controller.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app13.png" alt="Tabbed Application-13">

In the Document Outline, select the Item option, and in the Attributes Inspector, you will see that System item drop-down has some popular choices for a tab bar, like Favorites, History, and Bookmarks, etc. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app14.png" alt="Tabbed Application-14">

Select the History option; you can also provide your custom title and your custom image. Let's leave it as is and run your application. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app15.png" alt="Tabbed Application-15">

You can see now three tab items, click on the third option, and it will go to the third view.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/tabbed-app16.png" alt="Tabbed Application-16">
