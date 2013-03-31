---
layout: post
title: "Custom iOS Alert Dialogs"
date: 2013-03-30 19:48
comments: false
categories:
---
Sometimes when developing for iOS it's nice to show an alert dialog that has some functionality in addition to that available from the standard [UIAlertView](http://developer.apple.com/library/ios/#documentation/uikit/reference/UIAlertView_Class/UIAlertView/UIAlertView.html).

<!-- more -->
This additional functionality may take the form of including a text field on an alert or showing a progress indicator on the alert dialog.  Or, you may want to make the visual style of the alert match the rest of your app more closely.

A great library for assisting with these kinds of things is [CODialog](https://github.com/eaigner/CODialog) by [Erik Aigner](http://www.chocomoko.com).  The [Github project page](https://github.com/eaigner/CODialog) has some screenshots showing the kinds of things you can do with only a few lines of code.  The library is easy to include in your project since it consists of a single header file and a single implementation file.

In addition to the out-of-the-box examples, CODialog is easy to customize in other ways as well.  While I was working on [Baseball Field Layout](http://itunes.apple.com/us/app/baseball-field-layout/id604707759?ls=1&mt=8&partnerId=30&siteID=GedyEx6hBKQ) I wanted to create alert dialogs that matched the style of the app.  Using CODialog and customizing the implementation slightly, I was able to get alerts that looked just the way I wanted.

<div class="screenshots centered">
    {% img http://pictures.seancarpenter.net/blog/field_layout_alert.png 'Baseball Field Layout alert' 'Baseball Field Layout alert' %}
    <em>A Baseball Field Layout alert</em>
</div>

This can certainly be taken too far and you should always keep the iOS [Human Interface Guidelines](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html) in mind.  But for adding that missing piece of functionality or adapting the display of alerts, trying something a bit different can have a big effect on your app.
