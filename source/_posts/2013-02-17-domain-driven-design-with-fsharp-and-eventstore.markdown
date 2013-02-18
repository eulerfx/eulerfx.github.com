---
layout: post
title: "Domain-Driven Design with F# and EventStore"
date: 2013-02-17 19:04
comments: true
categories: [DDD,F#,EventStore,event sourcing] 
---
_In this post I present a methodology for implementing Domain-Driven Design (DDD) using F# and [EventStore](http://geteventstore.com/). The described approach makes use of idiomatic functional constructs, especially where they bring value beyond the traditional object-oriented and procedural paradigms. In this manner, the functional and object-oriented paradigms are contrasted. The natural alignment functional programming and event sourcing is exploited and implemented with EventStore. Note that this project is a spike and is a work in early stages of progress._

Implementing a design in a programming language once the model is conceived in the mind is often trivial by comparison. As a result, over time, the apparent value of a programming language can become diminished. Regardless of the added features in the next version of C# or Java, they all seem like mere syntactic sugar. F# and the functional paradigm have shifted my perspective on this. 

The ambitions of both Domain-Driven Design and functional programming can be regarded as one and the same - that of streamlining the representation of domain knowledge in a formal system with the goal of enabling automation and integration. Functional programming emphasizes the use of functions, expressions, immutability and algebraic data structures. These building blocks can be used for both the expression of domain knowledge and the furnishing of a framework for persistent execution of use cases.

The example in this post is based on [SimpleCQRS](https://github.com/gregoryyoung/m-r) by [Greg Young](http://goodenoughsoftware.net/). An existing F# implementation of this project is [SimpleCQRS-FSharp](https://github.com/Thorium/SimpleCQRS-FSharp) by [Tuomas Hietanen](https://github.com/Thorium). A more idiomatic F# implementation is [FsSimpleCQRS](https://github.com/thinkbeforecoding/m-r) by [Jérémie Chassaing](https://github.com/thinkbeforecoding). The example in this post aims to further free the implementation from establishmentarianist object-oriented practices. In fact, the use of classes is contained to integrations with the serialization library and the EventStore API. Classes were avoided in order to explore the benefits and drawbacks of the alternative. Caution was used to prevent focus on technology for the sake of technology alone. As such, the use of functional techniques are entirely justified.

## Analysis

The core of an OOP DDD implementation consists of the classes which represent entities, value objects and domain services. These elements embody the informational core of the project. The remaining components integrate the informational core with infrastructure that connects domain elements to databases for persistence, UIs for user interaction and other services.

### Domain Module

The informational core in this example is contained in the [InventoryItem](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/InventoryItem.fs) module:

{% gist 4975035 %}

This single F# source file contains the representation of *InventoryItem* aggregate state as well as the commands and events associated with the aggregate. Together, these elements capture the entirety of the core domain logic associated with an inventory item. Compared to both the original C# implementation and the referenced F# implementations, this implementation attains a greater degree of encapsulation. By looking at this module alone one could glean an understanding of the functionality of the domain. Furthermore, the use of discriminated unions ensures that all commands and events are explicitly handled with static verification. Unions bring the additional benefit of a uniform interface which is exploited by the *Aggregate* module described in the next section. 

There is no need to an aggregate base class because persistence of state is delegated to infrastructural components defined in outer layers. The identity value is taken out of the domain module because the domain logic itself has no need for it. This allows the identity value type to be defined in outer layers. Once we let go of the aging object-oriented and procedural practices we observe that they are only one of the many options.

### Aggregate Module

The [Aggregate](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/Aggregate.fs) module declares an abstraction of an aggregate and uses said abstraction to define a persistent command handler:

{% gist 4975076 %}

The aggregate abstraction is made possible by the use of a uniform interface for executing commands upon aggregate state. Application of persisted events is made with the [fold higher-order function](http://en.wikipedia.org/wiki/Fold_\(higher-order_function\)) - another staple of functional programming. The [IntegrationTests](https://github.com/eulerfx/DDDInventoryItemFSharpW/blob/master/DDDInventoryItemFSharp/IntegrationTests.fs) module employs the aggregate module to define a command handler by wiring the EventStore and the serialization module. 

### EventStore Module

The [EventStore](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/EventStore.fs) module contains the integration with EventStore. This initial implementation is specific to *InventoryItem* however it can be easily generalized by generalizing the serializer. 

## Summary

The F# implementation in this post is more concise, more encapsulated, more statically verified, and contains far less noise. I would argue that it is also more readable. Explicit factoring by functions elegantly replaces implicit factoring by class hierarchies. This establishes a dependency topology where the informational core is at the center with infrastructural and state related components orbiting in outer layers. 

In subsequent iterations, I will expand this example to contain query support via projections and read-models as well as a service layer. A more complex domain will further test this methodology. Specifically, I would like to explore the viability of these techniques with the introduction of behaviors bearing dependencies on domain services and behaviors involving complex invariants. Moreover, I would like to dig deeper into the use of F# types for representing domain state as described in [The Designing with types series](http://fsharpforfunandprofit.com/series/designing-with-types.html). 

F# paves the way for a language-oriented programming paradigm. In particular, computation expressions support the redefinition of control flow constructs in arbitrary contexts. This technique has the potential to define a persistent context for workflows which execute domain logic. This will allow further isolation and encapsulation of the informational domain core.


## Source

The source code for this post can be found on [GitHub](https://github.com/eulerfx/DDDInventoryItemFSharp).




