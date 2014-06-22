---
layout: post
date: 2014-06-22 19:54:17
title: Swift type conversions
---

In Swift, the native `String`, `Array` and `Dictionary` types are implicitly bridged to the `Foundation` classes `NSString`, `NSArray` and `NSDictionary`. In practice, this means that any Cocoa API we call that takes a `Foundation` class can take the corresponding Swift type (I don't say class here, because in Swift, `String`, `Array` and `Dictionary` are structs).

It turns out that like many other things in Swift, this isn't some language magic that only Apple gets to use. We can also build such implicit bridges, thanks to an undocumented Swift feature: The `__conversion` function.

Let's say we want to build a `Point` structure. We will be using it in a context that doesn't involve Core Graphics, Sprite Kit or a similar framework that already uses `CGPoint`. In addition, we want to build the struct in a modern way using new features idioms like generics and tuples. But, we also want to use our new struct with Core Graphics APIs sometimes so we can take advantage of all the convenience functions we implemented (This is a contrived example, because points are so simple, but bare with me).

    struct Point {
        let x: Double, y: Double
    }

Let's define the implicit bridging in an extension:

    extension Point {
        func __conversion() -> CGPoint {
            return CGPoint(x: x, y: y)
        }
    }

Now it's possible to pass a `Point` to any method or function that takes a `CGPoint`.
    
    let p = Point(x: 1.0, y: 1.0)
    CGPointApplyAffineTransform(p, CGAffineTransformMakeRotation(M_PI))
    
The extension can even be defined in a different file in the context where we want to use `Point` as a `CGPoint`.

We can also overload the `__conversion` function and define many different conversions. Here's one that destructures `Point` into a `(Double, Double)` tuple.

    extension Point {
        func __conversion() -> (Double, Double) {
            return (x, y)
        }
    }
    
Now this works!

    let (x, y) = p

I believe this will come in handy, especially when we start writing more and more Swift and we want to isolate non-Swift and/or legacy API calls and classes.
