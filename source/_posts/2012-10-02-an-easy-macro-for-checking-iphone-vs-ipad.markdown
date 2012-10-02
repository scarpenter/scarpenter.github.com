---
layout: post
title: "An easy macro for checking iPhone vs. iPad"
date: 2012-10-02 17:07
comments: false
categories: 
---

When working on a universal iOS app, I occasionally have the need to check what type of device the app is currently running on. This is pretty simple
to do using the `currentDevice` static method of the
<a href="http://developer.apple.com/library/ios/#documentation/uikit/reference/UIDevice_Class/Reference/UIDevice.html">UIDevice</a> class. This gives
you a UIDevice instance from which you can check the `userInterfaceIdiom` property. If you only need to check in one or two places this is sufficient:
<!-- more -->

{% gist 3823381 basiccheck.m %}

But once you start repeating that code in more places, it feels like a lot of work to get right. So instead I created a simple macro named `IsPhone`
to encapsulate the check:

{% gist 3823381 Project-Prefix.pch %}

Now I can just use the macro in my code instead of repeating the full check:

{% gist 3823381 using_macro.m %}

In general I try to avoid using this macro at all though. In most cases I have separate view controllers for the iPhone and iPad versions of a screen
so this logic is unnecessary. Where I find it useful is in shared controllers (primarily settings-type screens that are shown as popovers on the iPad).
This allows me to take different actions on popover dismissal as well as return different responses to `shouldAutorotateToInterfaceOrientation` depending
on the current device.
