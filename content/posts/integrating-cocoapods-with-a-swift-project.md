---
title: "Integrating Cocoapods with a Swift project"
date: 2014-06-04T06:37:10+01:00
draft: false
toc: false
images:
tags:
  - ios
  - cocoapods
  - swift
---

As Apple introduced [Swift][swift], their new programming language, you probably wonder how easily you can integrate it with existing Objective-C libraries that are available via [CocoaPods][cocoapods].

[wwdc]:https://developer.apple.com/wwdc/
[swift]:https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/swift_programming_language/index.html

You begin with creating a `Podfile`:

```
pod 'MKUnits', '~> 2.0.0'
```

and running `pod install` to install pods in your project.

Nice, so far so good. [Cocoapods][cocoapods] successfully integrated with [Xcode 6][xcode-6] project. All we need to do now is to import our pod header file into [Swift][swift] project. How do you do that?

[cocoapods]:http://cocoapods.org/
[xcode-6]:https://developer.apple.com/xcode/

Definitely not by adding `#import <MKUnits/MKUnits.h>` to a [Swift][swift] file as it would result in this error:

    Expected expression
    Expected identifier in import declaration

To expose Objective-C files to [Swift][swift] you must use **Objective-C bridging header**, as [Mix and Match][mix] section of [Using Swift with Cocoa and Objective-C][using-swift] documentation explains.

[mix]:https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/buildingcocoaapps/MixandMatch.html
[using-swift]:https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/buildingcocoaapps/index.html

To do so, you must create a new Objective-C header file, let's call it `Example-Bridging-Header.h`, and add it to the project. Then, you need to set `Objective-C Bridging Header` for your target:

![Xcode Bridging Header](/images/post-xcode-bridge.png)

and finally add import statement to the **bridge header**:

```swift
#import <MKUnits/MKUnits.h>
```

Voilà! Now you can use your pods in [Swift][swift].

```swift
let kilograms = NSNumber.mass_kilogram(2)()
let pounds = NSNumber.mass_pound(10)()
let result = kilograms.add(pounds)
println(result)
```

You can also integrate imported Objective-C classes with [Swift][swift] types. For example, by creating an extension for `Int`:

```swift
extension Int {

    func mass_kilogram() -> MKQuantity {
        return MKQuantity.mass_kilogramWithAmount(self)
    }

    func mass_pound() -> MKQuantity {
        return MKQuantity.mass_poundWithAmount(self)
    }
}
```

you can replace previous code with:

```swift
let kilograms = 2.mass_kilogram()
let pounds = 10.mass_pound()
let result = kilograms.add(pounds)
println(result)
```

## References

* [Swift and Objective-C in the Same Project][mix]
* [The Swift Programming Language][swift]
