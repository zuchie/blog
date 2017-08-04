---
layout: post
title:  Convert a decimal number to a binary number 
date:   2017-07-21 01:34:00 -0700
comments: true
categories: Swift 
---

![Alert](/assets/img/uiAlertController/alert.png)

UIAlertController is a subclass of UIViewController. If we want to present an alert, we have to present it by a UIViewController. In my project, I have a custom class which is neither of a type UIViewController nor a subclass of UIViewController. And I want to present alerts from this class. How do I do it?
<!--more-->

**A. Use recursion to find currently visible view controller as the presenting view controller.**
```swift
extension UIApplication {
  class func topViewController(controller: UIViewController? = UIApplication.shared.keyWindow?.rootViewController) -> UIViewController? {
    if let tabController = controller as? UITabBarController {
      return topViewController(controller: tabController.selectedViewController)
    }
    if let navController = controller as? UINavigationController {
      return topViewController(controller: navController.visibleViewController)
    }
    if let presented = controller?.presentedViewController {
      return topViewController(controller: presented)
    }
    return controller
  }
}
```
Then we can present alert:
```swift
UIApplication.topViewController()?.present(alertController, animated: false, completion: nil)
```

Referenced from [dianz's answer](http://stackoverflow.com/questions/26667009/get-top-most-uiviewcontroller). However, there may have corner case that the top view controller got from this recursion is not the view controller that we want to be the presenting view controller. For instance, view controller A presents view controller B, an alert is expected to be presented by A right after unwinding from B back to A. What we get from recursion would be B because at that time B is still in the process of being deinitialized. It still is the top view controller untill the deinitialization is fully completed. An error will occur because an alert is presented by a soon-to-be dismissed view controller.

**B. Define an alert protocol, ask the classes that need to present alert to conform to have a view controller property which will be used to present alerts.**
```swift
protocol AlertProtocol {
  weak var alertPresentingVC: UIViewController? { get set }
}

class ClassNeedsToPresentAlerts: AlertProtocol {
  weak var alertPresentingVC: UIViewController?
}
```

This approach resolves the corner case mentioned above because we can explicitly reference the presenting view controller property to the expected view controller. The drawback of this approach is that every class has to conform to the protocol and has to have this property. It also makes class decoupling hard to achieve because view controllers has to be passed around to where alerts are to be presented.

**C. Create a UIWindow with a UIViewController to present UIAlertController on it.**
```swift
extension UIAlertController {
  func show() {
    let window = UIWindow(frame: UIScreen.main.bounds)
    window.rootViewController = UIViewController()
    window.windowLevel = UIWindowLevelAlert
    window.makeKeyAndVisible()
    window.rootViewController?.present(self, animated: false, completion: nil)
  }
}
```

Inspired by [agilityvision's and Full Decent's answer](http://stackoverflow.com/questions/26554894/how-to-present-uialertcontroller-when-not-in-a-view-controller), adding a method to UIAlertController class to create a UIWindow with a UIViewController to present alert controller itself. So that any UIAlertController instance can present itself anytime anywhere independently. Hail to freedom! And this approach is said to be used by Apple internally.

I ended up using the last approach in my project. Its clear and self-contained nature is much appreciated. 
