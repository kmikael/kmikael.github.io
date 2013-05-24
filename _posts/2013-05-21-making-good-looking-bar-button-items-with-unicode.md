---
layout: post
date: 2013-05-21 00:00:00
title: Making Good-Looking Bar Buttons with Unicode
---

The standard `UIBarButtonSystemItem`'s are pretty limited and mostly boring, so we usually have to find a custom image and create a `UIBarButtonItem` with `initWithImage:style:target:action:`. This is, of course, great in most cases, except when you have limited resources and don't want to have too many images in your bundle.

## Unicode to the rescue

There are literally thousands of unicode characters available on iOS for you to use and it's, for the most part, simple to use them. For the standard behavior, just use `initWithTitle:style:target:action:` and set a unicode character as the title, but in cases where you need to customize the button even more, you could use something like this:

{% highlight objective-c %}
UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
button.frame = CGRectMake(0.0f, 0.0f, 32.0f, 32.0f);
[button setTitle:title forState:UIControlStateNormal];
button.titleLabel.font = [UIFont systemFontOfSize:20.0f];
button.titleLabel.shadowColor = [UIColor blackColor];
button.titleLabel.shadowOffset = CGSizeMake(0.0f, -1.0f);
[button addTarget:target action:action forControlEvents:UIControlEventTouchUpInside];
button.showsTouchWhenHighlighted = YES;
UIBarButtonItem *barButtonItem = [[UIBarButtonItem alloc] initWithCustomView:button];
{% endhighlight %}

It's that simple. Let's refactor it into a category and add the ability to invert the unicode symbol, which is useful for creating butons with matching arrows.

{% highlight objective-c %}
@interface UIBarButtonItem (PBAdditions)

+ (UIBarButtonItem *)barButtonItemWithTitle:(NSString *)title
                                   inverted:(BOOL)inverted target:(id)target
                                     action:(SEL)action;
                                     
+ (UIBarButtonItem *)barButtonItemWithTitle:(NSString *)title
                                     target:(id)target
                                     action:(SEL)action;
                                     
@end

@implementation UIBarButtonItem (PBAdditions)

+ (UIBarButtonItem *)barButtonItemWithTitle:(NSString *)title
                                   inverted:(BOOL)inverted target:(id)target
                                     action:(SEL)action
{
    UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
    button.frame = CGRectMake(0, 0, 32, 32);
    [button setTitle:title forState:UIControlStateNormal];
    button.titleLabel.font = [UIFont systemFontOfSize:20.0f];
    button.titleLabel.shadowColor = [UIColor blackColor];
    button.titleLabel.shadowOffset = CGSizeMake(0.0f, -1.0f);
    if (inverted) {
        button.titleLabel.transform = CGAffineTransformMakeRotation(M_PI);
    }
    [button addTarget:target action:action forControlEvents:UIControlEventTouchUpInside];
    button.showsTouchWhenHighlighted = YES;
    UIBarButtonItem *barButtonItem = [[UIBarButtonItem alloc] initWithCustomView:button];
    return barButtonItem;
}

+ (UIBarButtonItem *)barButtonItemWithTitle:(NSString *)title
                                     target:(id)target
                                     action:(SEL)action
{
    return [self barButtonItemWithTitle:title inverted:NO target:target action:action];
}

@end
{% endhighlight %}

Here are a few ideas to get you started:

- Settings (⚙)
- Slide-out Menu (☰)
- Forward button (➜) (invert the symbol for a back button)
- Add/Cancel (✚, ✖)
- Star/Unstar (★, ☆)
