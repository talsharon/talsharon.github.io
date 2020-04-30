---
layout: post
title: Optionals Explained ❓
---

Any *Swift* developer is familiar with *Optionals*. *Optionals* are *Swift* method of ensuring when a variable is accsessed, its value is not *nil*.
This allows us to access the variable safely, making our code clean and consistent.
But how is this mechanism implemented?
If we examine the *Swift* language repository, we'll find out that *Optional* is actaully implmeneted as an *enum*!
 
# 🤯

Swift defines optional as following:

```swift
@frozen //1
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
3. The *empty* case - this cases reperesents the *nil* value state.
4. The *value* case. This case is used when the optional has a value. 
   The value is passed as an assiciated value to the *some* case. It is of type *Wrapped*.

When you type *?* right after a type name, swift compiler translates it to an optional of that type.

### Extending Optional

Now that we know how *Optional* is implmeneted, we can have some fun with it.
Sometimes we want to check if our optional has a value, but we don't need the value itself.
We have two options:
1. Use anonymous unwrapping:
```swift
if let _ = ourOptionalVar { /* do stuff */ }
```
2. Check explicitly for nil:
```swift
if ourOptionalVar != nil { /* do stuff */ }
```

I personally hate to type *nil* checking every single time. It would be great if I could have xCode auto complete *nil* checks for me.  
Solution: let's extend *Optional* so it would have a *nil* checker member! 

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
1. We add an extension to *Optional*. We learnt that it is an enum, and enum can be extended as any other swift type.
2. We define a computed property *isNil*. Internally, we switches over *self* and returns *true* for *.none* case, and *false* for *.some* case.

Now we can easily use this new var to query for *nil*, and the best part, xCode will auto-complete our code!

```swift
var myOptionalVar: String?
if !myOptionalVar.isNil {
   // do stuff
}
```

we can do even better. Let's add a reversed var which will qeury if our optional has a value:

```swift
extension Optional {
  
  var hasValue: Bool { // 1
    return !isNil // 2
  }
}
```
Let's go over the code above:
1. We define a new computed property *hasValue*.
2. We use *isNil* and just negate its value.

We manifested swift code composition, and now our code is consistent and easy to maintain.
In case we'll want to change our implemantation in the future, we will only have to change *isNil* property and not both new properties.

now we can write:

```swift
var myOptionalVar: String?
if myOptionalVar.hasValue {
   // do stuff
}
```

### Conclusion

We now understand how Optionals are implemented in swift. We used our understanding to extend them to our own needs.
The fact Swift is open sourced allows us to examine its internal definitions and make better use of it 🚀