---
layout: post
title: "Scrolling a UITableView when displaying the keyboard"
date: 2012-10-15 12:51
comments: true
categories: 
published: false
---
## The problem
I have a few apps that allow you to edit text in a table cell of a <a href="http://developer.apple.com/library/ios/#documentation/uikit/reference/UITableView_Class/Reference/Reference.html">UITableView</a>. One thing that can happen when doing that (especially on the iPhone) is that when the keyboard is shown it can actually cover the text field being edited.
<!-- more -->

{% img http://pictures.seancarpenter.net/blog/initial_view.png 'Initial screen' 'Initial screen' %}
{% img http://pictures.seancarpenter.net/blog/cell_covered.png 'Edited cell covered by keyboard' 'Edited cell covered by keyboard' %}

Since that's obviously not good you need to scroll the edited cell into view when the keyboard is shown. My first attempt was to use the `scrollToRowAtIndexPath:atScrollPosition:animated:` method on `UITableView`. Unfortunately this is only part of the solution. While the table view will scroll the supplied row into view, it doesn't take into account the keyboard. In other words the table view scrolls the row into view according to the table view's frame, ignoring the fact that the keyboard is covering part of this frame. So the row may still not be visible.

The route I took is to change the table view's frame when the keyboard is shown. Combining this with a call to `scrollToRowAtIndexPath:atScrollPosition:animated:` results in the desired row being displayed when editing begins.

## Responding to the keyboard appearing
The first step is to be notified when the keyboard is shown or hidden. This is fairly straightforward to do by registering for the `UIKeyboardDidShowNotification`/`UIKeyboardDidHideNotification`.

{% gist 3866697 ViewController_RegisterNotifications.m %}

When we get the notification we need to do the adjustment of the frame:

{% gist 3866697 ViewController_Keyboard.m %}

First we save the initial height of the view. This is a handy shortcut that makes it easier to restore the initial size when the keyboard is hidden. On line 4 we grab the keyboard frame from the notification. This frame hasn't been adjusted for any transforms applied to the view (like rotation) so it needs to be converted to view coordinates (line 5). Now all that's left is to adjust the height of the main view. I just make the height of the main view equal to the `y` origin of the keyboard frame - this makes the view "touch" the top of the keyboard.

Restoring the views height when the keyboard is hidden is a straightforward affair. We just restore the view's initial height to what it was before shrinking it (which is why saving it in `keyboardShown:` was useful). The only additional step here is animating the view size change. While not strictly necessary, this makes the transition look a little nicer on the device.

## Finishing up
So now the table view's frame is being adjusted when the keyboard is shown. The last step is to scroll the newly resized table view to make the selected row visible. This is accomplished in the `UITextFieldDelegate`'s `textFieldDidBeginEditing` method. The only thing to be aware of here is that the delegate will be called before the keyboard is shown. To deal with that we delay the scrolling using delayed performance:

{% gist 3866697 ViewController_Scroll.m %}

On line 6 you'll need the row and section to scroll to - these need to be tracked/figured out based on your needs (they are the same values you would need if you weren't dealing with the view resizing).