---
layout: post
title:  "Getting Back to iOS Development Using Swift"
date:   2016-07-24 14:17:00 -0700
categories: Mac Swift iOS
---
After taking a break to actually get some work done, I am now returning to XCode to see if I can make some progress on my application. The app itself is not unlike any other business type of apps. I want to capture specific sets of data from the user, save that information to the cloud, then return the collection of data to the user in the form of graphs or charts.  Essentially, it is a progress application where the user will enter weights over time, and the application will display the trend in increased or decreased weight.  It's basic, simple and a good place to start learning to code in iOS.

I'll be using Firebase to capture the data, and return it back to the screen.  I'm not quite sure what I'll be using for the charts, but the first milestone will be to create an application to capture weight and save it to Firebase.

I started with the Single VIew application template in XCode and named it "WeightTracker".  I then created the application in my Firebase console, using the bundle name "com.rbdundas.WeightTracker", and copied the GoogleService-info.plist into the project.  I already have CocoaPods installed, so am not going to bother documenting that step here, but I will note that I ran the following in the terminal:

{% highlight shell %}
EM-U223415L001-2:WeightTracker 223415$ pod init
REM-U223415L001-2:WeightTracker 223415$ vi Podfile
REM-U223415L001-2:WeightTracker 223415$ pod install
Analyzing dependencies
Downloading dependencies
Installing Firebase (3.3.0)
Installing FirebaseAnalytics (3.2.1)
Installing FirebaseDatabase (3.0.2)
Installing FirebaseInstanceID (1.0.7)
Installing GoogleInterchangeUtilities (1.2.1)
Installing GoogleSymbolUtilities (1.1.1)
Installing GoogleUtilities (1.3.1)
Generating Pods project
Integrating client project

[!] Please close any current Xcode sessions and use `WeightTracker.xcworkspace` for this project from now on.
Sending stats
Pod installation complete! There are 2 dependencies from the Podfile and 7 total pods installed.
REM-U223415L001-2:WeightTracker 223415$
{% endhighlight %}
