---
layout: post
title: "Windows Phone 8 Makes It Hard To Get Started"
date: 2012-11-11 17:25
comments: false
published: false
categories: 
---
I decided to give Windows Phone 8 development a shot, enticed by their "$8 for 8 days" deal on developer program membership (it's normally $99/year).  My idea is to try writing [Easy Score Keeper](http://itunes.apple.com/us/app/easy-score-keeper/id570999111?ls=1&mt=8&partnerId=30&siteID=GedyEx6hBKQ) for Windows Phone 8 since I just released it on iOS.  Since I've been a Microsoft developer for 12 years and a .Net developer for about 9 I'm not too intimidated by coding in C#.

But it turns out that getting started with development is harder than I expected.  First off, I'm using almost entirely Macs now (even my wife's main computer is a MacBook Pro).  I thought I'd just use a [Parallels VM](http://www.parallels.com/products/desktop/) (like I do in my day-to-day work).  Unfortunately, the Windows Phone 8 emulator is actually a Hyper-V virtual machine so that adds some constraints to a development setup:

1. Hyper-V support in Windows 8 is [only available in the Pro edition](http://blogs.windows.com/windows/b/bloggingwindows/archive/2012/04/16/announcing-the-windows-8-editions.aspx) or higher, so a "plain" Windows 8 install already doesn't support running the emulator.
2. Your processor is required to support the relatively new [SLAT](http://en.wikipedia.org/wiki/Second_Level_Address_Translation) capability.

Since Parallels doesn't pass through any processor virtualization support I won't be able to do development in a VM (there are [reports](http://communities.vmware.com/message/2141330#2141330) of it working in an unsupported way in VMWare Fusion).  My next idea was to use a Windows 7 computer I have around and install Windows 8 in a VHD (per [Scott Hanselman's guide](http://www.hanselman.com/blog/GuideToInstallingAndBootingWindows8DeveloperPreviewOffAVHDVirtualHardDisk.aspx)).  But the computer I was going to do that on has an Intel Core 2 Quad processor which doesn't support SLAT, so that's out.

I guess I'll end up installing Bootcamp on my newest Mac (which has an Intel Core i5) and trying the development there.  That's suboptimal since now I need to reboot to switch between Windows Phone 8 development and doing anything else on my machine.

This decision seems odd considering that Windows Phone 8 is starting from behind in terms of developer adoption.  One way to get some apps in the store is to entice developers from other platforms to write for Windows Phone (either new apps or ports of existing ones).  But many developers (certainly iOS developers but I think most Android developers as well) are not running Windows natively on new hardware which makes the need for hardware supported virtualization cumbersome.  In addition, I know many Windows developers use VMs for development in order to manage separate environments, etc.  So this requirement is a problem for them as well.

The emulator requirements are also a problem if Microsoft is attempting to lure hobbyist developers.  Making a [Windows Phone 8 version](http://www.microsoft.com/visualstudio/eng/products/visual-studio-express-for-windows-phone) of Visual Studio Express free is a great way to get people interested (Visual Studio is a very full-featured IDE).  But the requirement for Windows 8 Pro means that basic machines being sold at retail stores won't be able to develop Windows 8 Phone apps without an upgrade.  The SLAT requirement also means that only machines purchased in the last 2-3 years (since the release of the Core i3 and i5) are capable of running the emulator.

Obviously iOS development has a high financial bar for entry since you need a Mac so this kind of requirement isn't unprecented.  But Apple isn't trying to kickstart a new platform at this point.  In addition, a 4 year old MacBook Pro is capable of doing development for iOS 6 so at least you don't need to go out and buy a brand new Mac if you have one around.

It's probably too late at this point but it would be great if Microsoft could remove the Hyper-V requirement from the Windows Phone 8 emulator.  Making it as easy as possible to develop for their platform can only help in the long run.