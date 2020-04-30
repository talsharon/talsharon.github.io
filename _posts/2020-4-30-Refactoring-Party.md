---
layout: post
title: Refactoring Party
---

Call me crazy, but I enjoy a good refactoring every now and then. In this post I am going to explain how I approach a refactoring task when I need to, and describe the refactoring process until I have a much better code in hand.

# 🧩

So you have a new high priority ticket assigned to you, titled "Do something that already works somewhere else in the app, but totally different". 'Easy', you smirk, and head over to xCode. You examine the code, and find this weird-named function, which you have to think really hard to understand what the hell it does. You tap 'go to defninition' and see a whole bunch of weird looking functions. 'OK, this code sucks' you think to yourself. 'Let's make it better'.

#### Identifying a refactor in need

```swift
import UIKit

class TransitionManager {
    
    static let initialScreen = "initial"
    static let todoListScreen = "todoList"
    static let todoDetailsScreen = "todoDetails"
    
    static func goToInitial() {
    }
}
```
