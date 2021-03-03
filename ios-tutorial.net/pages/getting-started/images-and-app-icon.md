# Images & Application Icon

In iOS, all the dimensions for placing any content on the screen is based on points, which map to pixels in the display. 

 - On a standard-resolution screen, or you can say on first original iPhones one point is equal to one pixel.
 - High-resolution displays (Retina iPhone devices) have a higher pixel density because there are more pixels in the same amount of physical space, there are more pixels per point. 
 - A standard resolution image has a scale factor of 1.0 also knows as a @1x image, and high-resolution images have a scale factor of 2.0 or 3.0 and are known as @2x and @3x images.

|Device                                                     |Scale factor   |
|:----------------------------------------------------------|:--------------|
|iPhone X, iPhone 8 Plus, iPhone 7 Plus, and iPhone 6s Plus |@3x            |
|All other high-resolution iOS devices                      |@2x            |

## Application/Home Screen Icon

All iOS applications you develop in Xcode will show up on the home screen using the default application icon.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/app-icon1.png" alt="App icon-1">

So whether you test your app on a physical device or just run the app in the simulator, you will see this generic icon, white with gridlines. Every app must supply small icons for use on the Home screen and throughout the system once your app is installed, as well as a larger icon for display in the App Store.

|Device           |Icon size                                                          |
|:----------------|:------------------------------------------------------------------|
|App Store        |1024px x 1024px (1024pt x 1024pt @1x)                              |
|iPhone           |180px x 180px (60pt x 60pt @3x)<br>120px x 120px (60pt x 60pt @2x) |
|iPad Pro         |167px x 167px (83.5pt x 83.5pt @2x)                                |
|iPad, iPad mini  |152px x 152px (76pt x 76pt @2x)                                    |

Let's go to the XCode and click on Assets.xcassets file. It is designed to make your life easier when you're working with images for iOS applications that are supposed to be provided at those multiple different scales.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/app-icon2.png"alt="App icon-2">

 - By default, there is a placeholder area here for you to put your application icon. 
 - You can provide multiple icons here, for whether the app will show up in the Spotlight search results, or if it will provide its settings and should be listed in the iOS Settings app, etc.
 - But the most important one is just the iPhone application icon, which for iPhone 7 (iOS 11) is 60 points, and needs to be provided at 2x scale and 3x scale. 

Select the 2x and open attributes inspector.

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/app-icon3.png" alt="App icon-3">

It will tell you, even more, this works for an iPhone, for an iPad, it's not relevant for the Apple Watch. It tells you what Size is and what Scale should be. 

We have two icons for our task list app, one of them at 120 x 120, and the other is 180 x 180. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/app-icon4.png" alt="App icon-4">

Let's drag the 3x into the 3x placeholder, the 2x into the 2x placeholder. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/app-icon5.png" alt="App icon-5">

Now run your application and tap the home button and you will see a new application icon. 

<img src="https://raw.githubusercontent.com/zzzprojects/iOS-Tutorial/master/docs/images/app-icon6.png" alt="App icon-6">
