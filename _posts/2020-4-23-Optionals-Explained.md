---
layout: post
title: Optionals Explained 🧐
---

Any *Swift* developer is familiar with *Optionals*. *Optionals* are *Swift* method of ensuring that when a variable is accessed, its value is not *nil*.
This allows us to access the variable safely, making our code clean and consistent.
But how is this mechanism implemented?
If we examine the *Swift* language repository, we'll find that *Optional* is actually implemented as an *enum*!  

<!--more-->
 
# 🤯

Swift defines *Optional* as follows:

```swift
@frozen // 1
public enum Optional<Wrapped>: ExpressibleByNilLiteral { // 2
  /// The absence of a value.
  ///
  /// In code, the absence of a value is typically written using the `nil`
  /// literal rather than the explicit `.none` enumeration case.
  case none // 3

  /// The presence of a value, stored as `Wrapped`.
  case some(Wrapped) // 4
}
```

Let's go over the code above:
1. The *@frozen* attribute means that this enum will not have more cases added to it in the future.
2. The *Optional* enum definition. It conforms to *ExpressibleByNilLiteral* protocol, which basically means that it can be initialized with *nil*. 
   Notice this enum is a generic enum using *Wrapped* as the generic type.
3. The *empty* case. this cases reperesents the *nil* value state.
4. The *value* case. This case is used when the optional has a value. 
   The value is passed as an associated value to the *some* case. It is of type *Wrapped*.

When you type *?* right after a type name, Swift compiler translates it into an *Optional* of that type.

### Extending Optional

Now that we know how *Optional* is implemented, we can have some fun with it.
Sometimes we want to check on whether our *Optional* has a value, but we don't need the value itself.
We have two options:
1. Use anonymous unwrapping:
```swift
if let _ = ourOptionalVar { /* do stuff */ }
```
2. Check explicitly for nil:
```swift
if ourOptionalVar != nil { /* do stuff */ }
```

I personally hate to type *nil* checking every single time. It would be great if xCode could auto-complete *nil* checks for me.  
Solution: Let's extend *Optional* so that it has a *nil* checker member! 

```swift
extension Optional { // 1
  
  var isNil: Bool { // 2
    switch self {
    case .none:
      return true
    case .some:
      return false
    }
  }
}
```

Let's go over the code above:
1. We add an extension to *Optional*. We learnt that it is an enum, and enum can be extended as any other Swift type.
2. We define a computed property *isNil*. Internally, we switch over *self* and return *true* for *.none* case, and *false* for *.some* case.

Now we can easily use this new var to query for *nil*, and the best part, is that xCode will auto-complete our code!

```swift
var myOptionalVar: String?
if !myOptionalVar.isNil {
   // do stuff
}
```

We can do even better. Let's add a reversed var which will query if our *Optional* has a value:

```swift
extension Optional {
  
  var hasValue: Bool { // 1
    return !isNil // 2
  }
}
```
Let's go over the code above:
1. We define a new computed property *hasValue*.
2. We use *isNil* and simply negate its value.

We manifested Swift code composition, and now our code is consistent and easy to maintain.
In case we'll want to change our implementation in the future, we will have to change only *isNil* property and not both new properties.

Now we can write:

```swift
var myOptionalVar: String?
if myOptionalVar.hasValue {
   // do stuff
}
```

### Conclusion

We now understand how Optional is implemented in Swift. We used our understanding to extend it to our own needs.
The fact that Swift is open sourced allows us to examine its internal definitions and make better use of it 🚀