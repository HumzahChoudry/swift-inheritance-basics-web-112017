# Swift — Inheritance Basics

## Objectives

1. Create a subclass in Swift.
2. Add a property to a subclass.
3. Add a method to a subclass.
4. Override a superclass's method with the `override` keyword.
5. Call the superclass's version of the method within an override using the `super` keyword.
6. Distinguish inheritance syntax from protocol conformance syntax.

## Inheritance

Swift offers **single inheritance** to its classes (and structs). The syntax for defining a class's superclass is pretty intuitive. It simply involves adding a colon `:` after the subclass's name and inserting the superclass's name before the opening curly brace that defines the class:

```swift
class SubclassName: SuperClassName {...}
```

### Inheriting From `NSObject`

While Swift's base classes don't require inheritance from `NSObject` as a default, it is still available as an option when creating a class. To create a Swift-y subclass of `NSObject` named `Ponso`, we would begin the class's definition like this:

```swift
class Ponso: NSObject {...}
```

That's pretty straightforward.

### Creating a Subclass

Let's declare a Swift base class named `Item` with a couple of properties and a customized print function:

```swift
class Item {
    var name = ""
    var priceInCents = 0
    
    func printInfo() {
        print("Item - name: \(name), price in cents: \(priceInCents)"
    }
}
```

Now let's subclass it into a new class `GroceryItem` and add an optional property for an expiration date:

```swift 
class GroceryItem: Item {
    var expiration: NSDate?
}
```

We can add a subclass method named `checkExpiration()` that will set the `priceInCents` to `0` if the expiration date has passed:

```swift 
class GroceryItem: Item {
    var expiration: NSDate?
    
    func checkExpiration() {
        if let expiration = expiration {
            let earlierDate = expiration.earlierDate(NSDate())
            if earlierDate == expiration {
                priceInCents = 0
            }
        }
    }
}
```
We're able to access the `priceInCents` property within `GroceryItem`'s definition since the property is inherited from the `Item` superclass.

### Overriding a Superclass Method

In order to override a superclass's method, the method must be declared on the subclass as usual, but be preceded by the `override` keyword to clearly distinguish it as an override:

```swift
class SubclassName: SuperclassName {
    override func nameOfMethodToOverride() {
        // new subclass implementation
    }
}
```

To override `Item`'s `printInfo()` method on `GroceryItem`, we could write something like this:

```swift
class GroceryItem: Item {
    var expiration: NSDate?
    
    override func printInfo() {
        print("GroceryItem - name: \(name), priceInCents: \(priceInCents), expiration: \(expiration)")
    }
    
    func checkExpiration() {...}
}
```

This will cause a different implementation of `printInfo()` to run instead when the method is called on an instance of `GroceryItem`.

#### Including the Superclass's Method During An Override

In certain cases (especially for view controller lifecycle methods such as `ViewDidLoad()`, `ViewDidAppear()`, etc.), the superclass's version of an overridden method can still be desirable or even necessary. In these cases, the superclass's method can be called by sending the method call to the `super` keyword:

```swift
class SubclassName: SuperclassName {
    override func nameOfMethodToOverride() {
        super.nameOfMethodToOverride()
        
        // subclass implementation
    }
}
```

To call the superclass's version of the `printInfo()` method from within an override on `GroceryItem`, we could write this like:

```swift
class GroceryItem: Item {
    var expiration: NSDate?
    
    override func printInfo() {
        super.printInfo()
    
        print("expiration: \(expiration)")
    }
    
    func checkExpiration() {...}
}
```

This version of the override will now print the `Item`'s information as normal, and *then* it print the additional line with the expiration date.

When working with Cocoa and Cocoa Touch, you will see this pattern of calling the superclass's implementation during an override:

```swift
class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
        
    }
}
```

## Inheritance vs. Protocol Conformance

The syntax for declaring conformance to a protocol is parallel to the syntax for declaring a superclass:

```swift
class BaseClassName: ProtocolName {...}
```
or

```swift
class SubclassName: SuperclassName, ProtocolName {...}
```

You may have already seen the syntax for this in the AppDelegate:

```swift
//    subclass     superclass   protocol
class AppDelegate: UIResponder, UIApplicationDelegate {...}
```

While this may have the initial appearance of presenting the `AppDelegate` class with "double inheritance", it actually is **not** (Swift only supports single inheritance); `UIApplicationDelegate` is a *protocol*.

**Note:** *If you're unfamiliar with the concept of protocols and delegates, we'll cover it later. For the time being, just recognize the distinction between superclasses and protocols despite the parallel syntax.*

The compiler will complain if you attempt to declare multiple superclasses:

```swift
class Ponso: NSObject, NSDictionary {...}  // error
```
![](https://curriculum-content.s3.amazonaws.com/swift/swift-inheritance-basics/error_no_multiple_inheritance.png)

Or, if the superclass is not listed first:

```swift
class Ponso: UITextFieldDelegate, NSObject {...}  // error
```
![](https://curriculum-content.s3.amazonaws.com/swift/swift-inheritance-basics/error_superclass_not_listed_first.png)
