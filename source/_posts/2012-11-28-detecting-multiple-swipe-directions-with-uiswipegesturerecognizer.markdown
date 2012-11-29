---
layout: post
title: "Detecting Multiple Swipe Directions with UISwipeGestureRecognizer"
date: 2012-11-28 17:13
comments: false
categories:
---
I recently had the need to use a [UIGestureRecognizer](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIGestureRecognizer_Class/Reference/Reference.html) for the first time in one of my iOS apps.  Specifically, I was interested in recognizing swipe gestures in one of my views so [UISwipeGestureRecognizer](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UISwipeGestureRecognizer_Class/Reference/Reference.html) was the obvious choice.

<!-- more -->

UISwipeGestureRecognizer is easier to configure than some of the more complicated recognizers since there are only two configurable properties: `direction` and `numberOfTouchesRequired`.  I was only interested in single touch swipes, so the default value of `1` was fine for `numberOfTouchesRequired`.  The `direction` property consists of a bit flag of `UISwipeGestureRecognizerDirection` values.  My first attempt was to just include the values for left and right gestures:

{% codeblock lang:objc %}
// This may not do what you want...
UISwipeGestureRecognizer* r = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipe:)];
r.direction = UISwipeGestureRecognizerDirectionRight | UISwipeGestureRecognizerDirectionLeft;
[self addGestureRecognizer:r];
{% endcodeblock %}

This works fine and swipes in both directions are recognized.  The problem comes when it's time to figure out which direction was swiped in the delegate method:

{% codeblock lang:objc %}
-(void) swipe:(UIGestureRecognizer*)recognizer {
    // Uh, what direction was swiped?
}
{% endcodeblock %}

There is no way to find out which swipe direction triggered the recognizer when the delegate message arrives.

The solution here is straightforward once you realize the problem: you just need to use two recognizers, each configured for a single direction:

{% codeblock lang:objc %}
UISwipeGestureRecognizer* right = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipeRight)];
right.direction = UISwipeGestureRecognizerDirectionRight;
[self addGestureRecognizer:right];

UISwipeGestureRecognizer* left = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipeLeft)];
left.direction = UISwipeGestureRecognizerDirectionLeft;
[self addGestureRecognizer:left];
{% endcodeblock %}

Now a different method (`swipeRight` or `swipeLeft`) will be called depending on which direction the user swipes.  This makes it easy to take the appropriate action in response.
