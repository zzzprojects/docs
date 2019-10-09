# Adaptive UI Using Traits and Size Classes

## Introduction

An adaptive UI means to adjust your content so that it fits well on any iOS device and that makes the best use of the available space. 

 - Auto Layout and stack views are essential when you are building flexible and adaptive user interfaces that can adjust themselves to work on multiple iOS devices.
 - So far, any actual change we make to a storyboard in one view is a change made to all views.
 - But if we want to make a change to one of these perspectives that would not affect the others. These kinds of changes cannot be done with just Auto Layout or stack views, but to use traits and Size classes.

## Traits

- Traits are another important component of the adaptive model. 
- It describes the environment in which your view controllers and views must operate. 
- It helps you make high-level decisions about your interface.

## Size Classes

In iOS, we use Size Class to deal with width and height which simpler and more generalized across multiple devices. 

 -There are only two options for an iOS size class, regular or compact.
 -Each iOS device has a default set of size classes that you can use as a guide when designing your interface. 

Below are some of the size classes for devices in both portrait and landscape orientations. 

| Device		 | Portrait (size class)                        | Landscape (size class)                		|
|:---------------|:---------------------------------------------|:----------------------------------------------|
| iPad (all)	 | Vertical: Regular <br> Horizontal: Regular	| Vertical: Regular <br>  Horizontal: Regular	|
| iPhone X	     | Vertical: Regular <br> Horizontal: Compact	| Vertical: Compact <br>  Horizontal: Compact	|
| iPhone 8 Plus	 | Vertical: Regular <br> Horizontal: Compact	| Vertical: Compact <br>  Horizontal: Regular	|
| iPhone 8	     | Vertical: Regular <br> Horizontal: Compact	| Vertical: Compact <br>  Horizontal: Compact	|
| iPhone 6 Plus	 | Vertical: Regular <br> Horizontal: Compact	| Vertical: Compact <br>  Horizontal: Regular	|
| iPhone 6	     | Vertical: Regular <br> Horizontal: Compact	| Vertical: Compact <br>  Horizontal: Compact	|
| iPhone 5/5c/5s | Vertical: Regular <br> Horizontal: Compact	| Vertical: Compact <br>  Horizontal: Compact	|
| iPhone 4s	     | Vertical: Regular <br> Horizontal: Compact	| Vertical: Compact <br>  Horizontal: Compact	|

Read more: [The Adaptive Model](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/TheAdaptiveModel.html)

