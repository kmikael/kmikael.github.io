---
layout: post
date: 2014-08-09 14:21:21
title: Modeling Shapes in Swift
---

A few friends and I were talking about functional programming vs. object oriented programming the other day and I decided to illustrate the concepts in Swift using a simple and classic example. Consider a shape model. There will be different shapes and we want to calculate each shape's perimeter and area.

An object oriented programmer might write it like this:

    let pi = 3.14159265358979323846264338327950288

    class Shape {
        func perimeter() -> Double {
            return 0.0
        }

        func area() -> Double {
            return 0.0
        }
    }

    class Circle: Shape {
        let radius: Double

        init(radius: Double) {
            self.radius = radius
        }

        override func perimeter() -> Double {
            return 2.0 * pi * radius
        }

        override func area() -> Double {
            return pi * radius * radius
        }
    }

    class Rect: Shape {
        let width: Double
        let height: Double

        init(width: Double, height: Double) {
            self.width = width
            self.height = height
        }

        override func perimeter() -> Double {
            return 2.0 * (width + height)
        }

        override func area() -> Double {
            return width * height
        }
    }

And then use it like so:

    let shapes = [
        Circle(radius: 1.0),
        Rect(width: 3.0, height: 4.0)
    ]

    let perimeters = shapes.map { shape in shape.perimeter() }
    let areas = shapes.map { shape in shape.area() }

`Shape` is an abstract superclass which declares the methods that subclasses should override.

To add a new shape, we simply create a new class and then implement each of `Shape`'s methods. To add a new computation, we first add a method to `Shape`, returning a default value, followed by implementing the method appropriately in each subclass.

In Swift, a very similar solution would be to use a protocol instead of an abstract superclass. This has the advantage that any existing class can be made into a shape and we can mix and match classes and structs if need arises. What we lose is the ability to have methods in the superclass that use the subclass methods to perform additional work, as Swift protocols cannot have default implementations.

    let pi = 3.14159265358979323846264338327950288

    protocol Shape {
        func surface() -> Double
        func area() -> Double
    }

    class Circle: Shape {
        let radius: Double

        init(radius: Double) {
            self.radius = radius
        }

        func surface() -> Double {
            return 2.0 * pi * radius
        }

        func area() -> Double {
            return pi * radius * radius
        }
    }

    class Rect: Shape {
        let width: Double
        let height: Double

        init(width: Double, height: Double) {
            self.width = width
            self.height = height
        }

        func surface() -> Double {
            return 2.0 * (width + height)
        }

        func area() -> Double {
            return width * height
        }
    }

A functional programmer might solve the problem like this:

    let pi = 3.14159265358979323846264338327950288

    enum Shape {
        case Circle(Double)
        case Rect(Double, Double)
    }

    func perimeter(shape: Shape) -> Double {
        switch shape {
        case let .Circle(r):
            return 2.0 * pi * r
        case let .Rect(width, height):
            return 2.0 * (width + height)
        }
    }

    func area(shape: Shape) -> Double {
        switch shape {
        case let .Circle(r):
            return pi * r * r
        case let .Rect(width, height):
            return width * height
        }
    }

And then use the constructs like so:

    let shapes = [
        Shape.Circle(1.0),
        Shape.Rect(3.0, 4.0)
    ]

    let perimeters = shapes.map { shape in perimeter(shape) }
    let areas = shapes.map { shape in area(shape) }

Here, we use an [algebraic data type](http://en.wikipedia.org/wiki/Algebraic_data_type). Then in each function, we pattern match on the shape, returning the appropriate value.

In this case, to add a new shape, we simply add a new case to the `Shape` enum and then add a case for that new shape to each function. To add a new computation, we add a new function, pattern match on the shape and return the appropriate value.

Additionally, Swift allows `enum`'s to have methods so it gives us the convenience of defining all the computations in a single context while keeping the code in a functional style.

    let pi = 3.14159265358979323846264338327950288

    enum Shape {
        case Circle(Double)
        case Rect(Double, Double)

        func perimeter() -> Double {
            switch self {
            case let Circle(r):
                return 2.0 * pi * r
            case let Rect(width, height):
                return 2.0 * (width + height)
            }
        }

        func area() -> Double {
            switch self {
            case let Circle(r):
                return pi * r * r
            case let Rect(width, height):
                return width * height
            }
        }
    }

This is basically the same as the functional example, but instead of having top level functions, all computations are nicely inside the `Shape` enum. And the usage is exactly the same as the object oriented case:

    let shapes = [
        Shape.Circle(1.0),
        Shape.Rect(3.0, 4.0)
    ]

    let perimeters = shapes.map { shape in shape.perimeter() }
    let areas = shapes.map { shape in shape.area() }

The main disadvantage of this setup is that it's now very difficult and inelegant to have specific functions that only operate on a specific shape. In the object-oriented style, we could just add methods to the `Circle` class for example, and in the functional case we could write functions that take a `Circle`.

Like most things there is no correct answer here. It's really nice that Swift provides many options.
