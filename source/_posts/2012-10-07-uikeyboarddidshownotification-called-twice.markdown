---
layout: post
title: "UIKeyboardDidShowNotification called twice"
date: 2012-10-07 19:46
comments: false
categories: 
---
In one of my iOS apps, I'm listening for the `UIKeyboardDidShowNotification` to do some view resizing when the keyboard is shown (a more detailed post on doing this is coming). Recently I noticed that my selector was being called twice when the keyboard was shown on iOS 6.

<!-- more -->

## Tracking it down
What made this a bit difficult to track down is that it was only happening in one of my view controllers that listened for this notification. In the other, the selector was only being called once as expected. My first step was to do some logging to figure out what keyboard frame was arriving in each notification:
{% codeblock lang:objc %}
-(void)keyboardShown:(NSNotification*) notification {
	CGRect keyboardFrame = [[[notification userInfo] objectForKey:UIKeyboardFrameEndUserInfoKey] CGRectValue];
	CGRect converted = [self.view convertRect:keyboardFrame fromView:nil];
	NSLog(@"Keyboard frame: %@", NSStringFromCGRect(keyboardFrame));
}
{% endcodeblock %}
The first thing this logging made obvious was that it was indeed a different frame being delivered in each notification. While the width was the same, the keyboard frame was taller in the second notification than the first. When I compared the log data from the view controller where the notification was arriving twice to the one where it was only arriving once, I found that the keyboard frame height matched the first of the double notifications.

## The cause
That height match was what finally caused the light bulb to go on for me. The `UITextField` that was triggering two notifications uses an `inputAccessoryView` to display a close button above the keyboard. The first notification that arrives contains the keyboard frame without the `inputAccessoryView` while the second includes the height of the accessory view. I've confirmed that this only happens on iOS 6 - on iOS 5 only a single notification is delivered that contains the keyboard frame including the accessory view.

## The solution
That really depends on what you are doing in your selector. If you are just resizing your main view you may be fine receiving both notficiations - your view will just end up being resized twice. If not you may need to keep track of the two notifications so that you know which one you are responding to.
