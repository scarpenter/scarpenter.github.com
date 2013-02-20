---
layout: post
title: "CLLocation Extensions"
date: 2013-02-20 16:51
comments: false
categories:
---
I've been working on a new iOS project that uses [CoreLocation](http://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html) to determine some points.  While [CLLocation](http://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocation_Class/CLLocation/CLLocation.html#//apple_ref/doc/uid/TP40007126) has a method for determining the distance between two locations (`distanceFromLocation:`) it doesn't have one for doing the opposite: calculating a second location based on a distance (and bearing) from the first.

<!-- More -->

So I added a category on CLLocation that performs this calculation:
{% codeblock lang:objc %}
#import <CoreLocation/CoreLocation.h>
#import "CLLocation+LocationExtensions.h"

CLLocation* startPoint = [[CLLocation alloc] initWithLatitude:39.90657 longitude:-75.16656];
CLLocation* newPoint = [startPoint locationAtDistance:300 andBearing:180];
NSLog(@"New point: %@", newPoint); // new point is at 39.90387204, -75.16656
{% endcodeblock %}

The other need I had was to calculate the bearing between two points:
{% codeblock lang:objc %}
#import <CoreLocation/CoreLocation.h>
#import "CLLocation+LocationExtensions.h"

CLLocation* startPoint = [[CLLocation alloc] initWithLatitude:39.90657 longitude:-75.16656];
CLLocation* endPoint = [[CLLocation alloc] initWithLatitude:39.90387204 longitude:-75.16656];
CLLocationDegrees bearing = [startPoint bearingTo:endPoint];
NSLog(@"Bearing: %f", bearing); // bearing is 180
{% endcodeblock %}

Both of these calculations use the formulas provided on the page [Calculate distance, bearing and more between Latitude/Longitude points](http://www.movable-type.co.uk/scripts/latlong.html).  This page has lots of information on the formulas and provides them in mathematical notation as well as examples translated to Javascript.

My Objective-C category incorporating these calculations is available from [my Github account](https://github.com/scarpenter/LocationExtensions).
