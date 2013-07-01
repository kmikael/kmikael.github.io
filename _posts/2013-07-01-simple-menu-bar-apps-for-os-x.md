---
layout: post
date: 2013-07-01 15:01:17
title: Making Simple Menu Bar Apps for OS X
---

It's pretty easy to make a simple menu bar app on OS X. The main thing you need to do is to create an `NSStatusItem` instance variable in your app delegate. To do this, you ask `[NSStatusBar systemStatusBar]` to create one for you, providing a `length`. In almost every case you will want to use `NSVariableStatusItemLength` instead of some fixed `float` value. Once you have an `NSStatusItem`, you can configure it using it's properties. I used some of the most relevant ones below:

{% highlight objective-c %}
@interface KMAppDelegate : NSObject <NSApplicationDelegate>
    
@property (strong, nonatomic) NSStatusItem *statusItem;
    
@end
{% endhighlight %}

{% highlight objective-c %}
_statusItem = [[NSStatusBar systemStatusBar] statusItemWithLength:NSVariableStatusItemLength];

// The text that will be shown in the menu bar
_statusItem.title = @"";

// The image that will be shown in the menu bar, a 16x16 black png works best
_statusItem.image = [NSImage imageNamed:@"feedbin-logo"];

// The highlighted image, use a white version of the normal image
_statusItem.alternateImage = [NSImage imageNamed:@"feedbin-logo-alt"];

// The image gets a blue background when the item is selected
_statusItem.highlightMode = YES;
{% endhighlight %}
    
Next, you will want something to happen when the status item is clicked. You have a few options here: You can have a menu be shown, you can configure a target-action pair and use the status item like a button or you can have a custom view be shown. See [the documentation](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/ApplicationKit/Classes/NSStatusItem_Class/Reference/Reference.html) for all the details. Here, we use a simple menu and attach it to the status item.

{% highlight objective-c %}
NSMenu *menu = [[NSMenu alloc] init];
[menu addItemWithTitle:@"Open Feedbin" action:@selector(openFeedbin:) keyEquivalent:@""];
[menu addItemWithTitle:@"Refresh" action:@selector(getUnreadEntries:) keyEquivalent:@""];
if ([[[KMFeedbinCredentialStorage sharedCredentialStorage] credential] hasPassword]) {
    [menu addItemWithTitle:@"Log Out" action:@selector(logOut:) keyEquivalent:@""];
} else {
    [menu addItemWithTitle:@"Log In" action:@selector(logIn:) keyEquivalent:@""];
}
[menu addItem:[NSMenuItem separatorItem]]; // A thin grey line
[menu addItemWithTitle:@"Quit Feedbin Notifier" action:@selector(terminate:) keyEquivalent:@""];
_statusItem.menu = menu;
{% endhighlight %}

The rest is all up to you. You can pretty much set up a simple app that connects to a web service and shows some information in an hour or two.

Note that you will probably want to have the app not show up in the dock. To do this you need to set the `LSUIElement` key to `YES` in your `Info.plist` configuration file. You should see *Application is agent (UIElement)* set to *YES* if you have done it correctly.

The sample code I used is an app I wrote to show me the number of unread RSS items in my [Feedbin](https://feedbin.me) account. You can check it out [on GithHub](https://github.com/kmikael/FeedbinNotifier). It pretty much creates a status item in the app delegate and then refreshes the unread count every two minutes using an `NSTimer`. Before that, it asks the user to log in and then saves the user's password in the system keychain, so there are a few helper classes in addition to the app delegate. The whole app is comprised of a mere 202 lines of Objective-C code according to [cloc](http://cloc.sourceforge.net).
