# Launch Screen

A launch screen appears instantly when your app starts up and it is immediately replaced with the first screen of your application, giving the impression that your app is fast and responsive. In XCode, you may have noticed that when you create a project you will have not only the Main.storyboard, but also LaunchScreen.storyboard.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/launch-screen1.png" alt="launch screen-1">

From the name you can guess that this has something to do with the application launching.

 - It is used for that split-second zooming effect you see when you tap an application's icon on the home screen. 
 - When this zoom occurs, you are not seeing the real application zooming in, this is a cosmetic fiction. 

## What to Show on Launch Screen?

The launch screen is not an opportunity for artistic expression, but It is solely intended to enhance the perception of your app as quick to launch and immediately ready for use. 

 - It is supposed to stay very minimal, because it is not designed for you to use as a splash screen and put a bunch of artwork on.
 - You can provide your launch screen as images, but it is often easier to just make a simple storyboard, and iOS will use Auto Layout and size classes to make sure what is shown on the launch screen adapts to the device. 
 - You don't need to add any text, content or code, but it is just for UI elements to provide a very basic screen similar to your application home screen.

Let's open the XCode and replicate what you have on your Main.storyboard.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/launch-screen2.png" alt="launch screen-2">

It has pretty much nothing except the toolbar, because the table view will be empty initially. So go to the LaunchScreen.storyboard and drag a toolbar to the bottom.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/launch-screen3.png" alt="launch screen-3">

By default, a toolbar contains an Item and on Launch screen we don't need any text or content, so remove that item. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/launch-screen4.png" alt="launch screen-4">

Our regular interface has a switch on it, add that and here the initial state of the switch is on so that you can see that actually the LaunchScreen.storyboard is shown for a fraction of seconds when you run your application.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/launch-screen5.png" alt="launch screen-5">

Now you will not see a blank screen initially but you will see that launch storyboard for a moment.
