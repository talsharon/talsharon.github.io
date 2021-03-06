---
layout: post
title: Custom operators 🥳
---

Swift lets us easily define custom operators. I am going to demonstrate how it is done using a simple use case - managing and layouting views.

<!--more-->

# 💯

### Simplifying code

Manipulating views in code usually involves a lot of boilerplate code. We have to think about dimensions, frames, or constraints. Apple doesn't give us a break here, as auto-layout API offered by UIKit is not trivial and not very natural.
Luckily for us, we can make it better by using custom operators!  
Consider the following code:

```swift

  let parent = UIView(frame: CGRect(x: 0, y: 0, width: 100, height: 100)) // 1
  let child1 = UIView()
  let child2 = UIView()
  let child3 = UIView()
  
  parent.addSubview(child1) // 2
  child1.translatesAutoresizingMaskIntoConstraints = false // 3
  child1.widthAnchor.constraint(equalToConstant: 50).isActive = true // 4
  child1.heightAnchor.constraint(equalToConstant: 50).isActive = true
  child1.topAnchor.constraint(equalTo: parent.topAnchor).isActive = true
  child1.trailingAnchor.constraint(equalTo: parent.trailingAnchor).isActive = true
  
  parent.addSubview(child2)
  child2.translatesAutoresizingMaskIntoConstraints = false
  child2.widthAnchor.constraint(equalToConstant: 40).isActive = true
  child2.heightAnchor.constraint(equalToConstant: 30).isActive = true
  child2.topAnchor.constraint(equalTo: parent.topAnchor).isActive = true
  child2.leadingAnchor.constraint(equalTo: parent.leadingAnchor).isActive = true
  
  parent.addSubview(child3)
  child3.translatesAutoresizingMaskIntoConstraints = false
  child3.heightAnchor.constraint(equalToConstant: 20).isActive = true
  child3.topAnchor.constraint(equalTo: parent.topAnchor).isActive = true
  child3.trailingAnchor.constraint(equalTo: parent.trailingAnchor).isActive = true
  child3.leadingAnchor.constraint(equalTo: parent.leadingAnchor).isActive = true

```

Consider how much boilerplate code we have to use in order to set up UI elements programmatcially:
1. We have to create views with explicit frames and dimensions.
2. We have to add views to other views.
3. Since we want to use UIKit auto layout constraints, we need to tell views to not deduce implicit constraints based on the initial frame.
4. We have to add layout constraints using Apple's far-from-human-readable syntax.

Imagine doing this for each screen in your app, for multiple views. 

### Defining custom operators

It would be nice if we could easily add subviews to each other. Let's define a custom operator for this purpose:

```swift

  infix operator +== // 1

  func +==(_ parent: UIView, _ child: UIView) { // 2
    parent.addSubview(child) // 3
  }

```

1. We define an infix operator, which means it goes between two operands. We choose *+==* as the operator symbol.
2. The operator implementation is a func which takes two arguments, *parent* as the left-hand-side argument, and *child* as the right-hande-side argument.
3. We add *child* as the parent subview.

Now we can write:

```swift

  parent +== child1
  parent +== child2
  parent +== child3

```

Let's improve this operator even more. It would be nice if we could add all subviews in a single line rather than have a separate line for every subview.

```swift

  precedencegroup UIViewAddSubviewPrecedence { // 1
    associativity: left
  }

  infix operator +==: UIViewAddSubviewPrecedence // 2
  @discardableResult // 3
  func +==(_ parent: UIView, _ child: UIView) -> UIView {
    parent.addSubview(child)
    return parent // 4
  }

```

1. We define a new precedence group, and define its associativity as *left*. This means that when the compiler has to evaluate multiple operators in sequence, it will go from left to right.
2. We conform to our new precedence group.
3. We mark the operator result as *@discardableResult*. This tells the compiler that the use of the operator result is not mandatory and eliminates warnings about not using it.
4. We return the parent as the operator result.

Now we can write:

```swift

  parent +== child1 +== child2 +== child3

```

### Auto layout - give me some sugar

We still didn't solve the boilerplate code involving the use of auto-layout constraints.
We will achieve this in two steps.
The first step is to define a few helper functions to encapsulate the boilerplate code (and be able to forget about it altogether :)).

```swift

extension UIView {
  
  func setHeight(_ height: CGFloat) {
    translatesAutoresizingMaskIntoConstraints = false
    heightAnchor.constraint(equalToConstant: height).isActive = true
  }
  
  func setWidth(_ width: CGFloat) {
    translatesAutoresizingMaskIntoConstraints = false
    widthAnchor.constraint(equalToConstant: width).isActive = true
  }
    
  func pinLeading(to view: UIView) {
    translatesAutoresizingMaskIntoConstraints = false
    leadingAnchor.constraint(equalTo: view.leadingAnchor).isActive = true
  }
  
  func pinTrailing(to view: UIView) {
    translatesAutoresizingMaskIntoConstraints = false
    trailingAnchor.constraint(equalTo: view.trailingAnchor).isActive = true
  }
    
  func pinTop(to view: UIView) {
    translatesAutoresizingMaskIntoConstraints = false
    topAnchor.constraint(equalTo: view.topAnchor).isActive = true
  }
}

```

We extend UIView and add multiple functions with a human readable syntax which defines layout constraints internally.

In the next step, we will define more custom operators which will use these functions:

```swift

  infix operator |<- // pin to leading edge
  infix operator ->| // pin to trailing edge
  infix operator ^^ // pin to top
  infix operator ||| // set height
  infix operator --- // set width

  func |<-(_ lhs: UIView, _ rhs: UIView) {
    rhs.pinLeading(to: lhs)
  }

  func ->|(_ lhs: UIView, _ rhs: UIView) {
    lhs.pinTrailing(to: rhs)
  }

  func ^^(_ lhs: UIView, _ rhs: UIView) {
    lhs.pinTop(to: rhs)
  }

  func |||(_ view: UIView, _ height: CGFloat) {
    view.setHeight(height)
  }

  func ---(_ view: UIView, _ width: CGFloat) {
    view.setWidth(width)
  }

```

Now we can do lots of view manipulations using our brand new operators:

```swift

  child1 ||| 50
  child1 --- 50
  child1 ->| parent
  child1 ^^ parent

  child2 ||| 30
  child2 --- 40
  child2 |<- parent
  child2 ^^ parent

  child3 ||| 20
  child3 ^^ parent
  child3 |<- parent
  child3 ->| parent

```

Pretty cool 😎

### Conclusion

We've learnt how we can define custom oeprators in SWift, and how we could use them for making boiler plate code much cleaner. ⭐️
