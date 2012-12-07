---
layout: post
title: "UIPageControl Doesn't Appear"
date: 2012-12-06 18:37
comments: false
categories:
---
I had a little trouble with my first use of a [UIPageControl](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIPageControl_Class/Reference/Reference.html).  I added it to my view in a XIB file and set the "Tint Color" and "Current Page" properties to dark colors.  Everything looked great in the XIB but when I ran the app, the pager wasn't there.

<!-- more -->
A couple of Google searches led to a [few](http://stackoverflow.com/a/9612497/729) [posts](http://stackoverflow.com/a/6016125/729) mentioning the fact that the pager looks like it's invisible when shown on a light background.  This is a consequence of the default colors being variations of white.  I dismissed this as being my issue since I had set the colors to be dark in the XIB and everything looked fine there.

After much messing around with positioning, colors, etc. (everything I could think of) I went back to [Apple's documentation](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIPageControl_Class/Reference/Reference.html).  The `currentPageIndicatorTintColor` and `pageIndicatorTintColor` properties are only available in iOS 6.0 and above.  Since I was developing with the iOS 6 SDK, the properties were settable in the XIB.  But I was running the app on an iOS 5.1 device which didn't have the properties, so the pager was in fact white.  I solved the problem by setting the pager colors back to the default in the XIB and displaying the pager on a dark background.

Two lessons here: 1) When something doesn't seem to be working, it never hurts to give the documentation another look, and 2) It's important to test your app on whatever iOS/device versions you'll actually be deploying to.  While this particular issue doesn't result in a runtime crash, having the pager be invisible to anyone running iOS 5 would not have been a good outcome.