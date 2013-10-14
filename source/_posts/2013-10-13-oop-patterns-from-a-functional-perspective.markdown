---
layout: post
title: "Object-oriented design patterns from a functional perspective"
date: 2013-09-18 21:50
comments: true
categories: [OOP,FP]
---
_In this post I interpret design patterns and principles commonplace in [object-oriented programming](http://en.wikipedia.org/wiki/Object-oriented_programming) languages from the perspective of [functional programming](http://en.wikipedia.org/wiki/Functional_programming). Most of these patterns are trivially reduced to elementary functional constructs. The intent is to illustrate advantages and insight resulting from a shift in paradigm. After all, both the object-oriented and the functional paradigm have a shared goal - the solution of some problem. The patterns discussed herein are a testament to this commonality. The difference between the paradigms lies in the underlying abstractions upon which they are established which in turn have significant ramifications for the problem solving tactics they engender. In particular, I argue that the object-oriented inheritance model and the paradigm's primary utilization as an adorned state encapsulation mechanism are limiting factors in composing higher-level abstractions. Conversely, functional programming, being based on an entirely different [model of computation](http://en.wikipedia.org/wiki/Model_of_computation), eschews state, thereby bypassing accompanying hurdles and mandates composion from the core, thereby encouraging resuable abstractions._ 

<!--more-->

## Introduction

The following claims are based on a few years of experience developing applications with the functional paradigm. In particular, contrasts of the paradigms have been distilled through porting code samples for [Implementing Domain-Driven Design](http://www.amazon.com/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577) from a [Java/C# implementation](https://github.com/VaughnVernon/IDDD_Samples_NET) to an [implementation in F#](https://github.com/eulerfx/IDDDFSharpSamples). 

## Disclaimers

The analysis of the object-oriented paradigm herein is based particularly on its manifestation in languages like Java, C# and related languages. ([Variations of object-oriented programming without state have characteristics orthogonal to abstract data types (ADTs) employed in functional programming](http://www.cs.utexas.edu/~wcook/papers/OOPvsADT/CookOOPvsADT90.pdf)). Functional programming examples are provided in F#.

This topic has been addressed priorly. Peter Norvig [discussed](http://www.norvig.com/design-patterns/) how most of the _GoF_ patterns are _invisible or simpler_ in LISP, calling the patterns *"programming language bug reports"*. Paul Graham [echoes](http://www.paulgraham.com/icad.html) the notion. Similarly, Joe Armstrong describes [why OO sucks](http://harmful.cat-v.org/software/OO_programming/why_oo_sucks). Although both LISP and Erlang (designed by Armstrong) are dynamically type-checked, the arguments hold just as well for statically typed languages. In fact, the issue of typing is orthogonal.

Arguably, the power of abstraction in functional programming can be a double edged sword. For example, a [monoid](http://apocalisp.wordpress.com/2010/06/14/on-monoids/) is in essence a very simple abstraction - an interface consisting of two members. However, without some context, it can be troublesome to sense its value and utility. Abuse of such abstractions can lead to cryptic code, however this isn't representative of the functional approach.

Finally, the intent herein is not to undermine the object-oriented paradigm. In many ways, well crafted object-oriented code converges upon a functional style. This is no surprise because as stated earlier, the eventual goals of both paradigms are one and the same. It should also be stated that given the present state of the art, functional programming isn't practical for all domains.

## Overview

In this post I survey the [SOLID](http://en.wikipedia.org/wiki/SOLID_\(object-oriented_design\)) patterns followed by tactical patterns of [Domain-Driven Design](http://en.wikipedia.org/wiki/Domain-driven_design). These patterns are intimately related. The single-responsibility principle fosters the interface-segregation principle; the open/closed principle fosters the Liskov substitution principle. The dependency inversion principle fosters Domain-Driven Design.

The central grievances with object-oriented programming as presented herein are summarized as follows.

- Binding data structure to behavior is a mechanisms of state encapsulation which hides the underlying problem instead of solving it.
- A great deal of effort goes into making inheritance possible. Ironically, object-orientated patterns themselves favor composition over inheritance. Ultimately, in being a jack of two responsibilities - subtyping and reuse - inheritance is a master of neither and does a inferior job with both.

The distinguishing characteristics of functional programming that address these grievances are summarized as follows.

- Explicit management of state is avoided through immutability.
- Explicit return values are favored over implicit side effects.
- Powerful composition facilities promote reuse without compromising encapsulation.
- The culmination of these characteristics is a more declarative paradigm.



## <a id="solid"></a>SOLID

### <a id="single-responsibility-principle"></a>Single-responsibility Principle

The [single-responsibility principle](http://en.wikipedia.org/wiki/Single_responsibility_principle) states that _every class should have a single responsibility_ where a responsibility is roughly defined as a reason to change. The principle compensates for the anti-pattern where bloated classes play multiple roles. Classes can bloat for a few reasons. A core principle of object-oriented programming is the binding of data structure to behavior. The problem is that optimizing for data structure encapsulation not only weakens composition characteristics, but also hides the underlying problem of explicit state. As a result, object-oriented code typically contains many data structures with relatively few functions per data structure. Adding methods to a class exerts pressure on the single-responsibility principle and reducing the number of methods can either make the data structure difficult to compose or all together fruitless. Furthermore, the simple syntactical cost of declaring a class often compels programmers to marginalize. In less verbose languages, particularly dynamic ones like Ruby and Python, this problem is less prevalent. In my opinion, the importance of this purely mechanical issue must not be understated. A great deal of effort is put into optimizing development with IDEs and other tools, yet optimization can often be achieved at a far more fundamental level.

In functional programming, the fundamental unit of abstraction is the function. Given that a function has a single input and a single output, functions naturally have a single responsibility. One could certainly define arbitrarily generic function, though this would be counterintuitive. Moreover, functions are syntacticly thrifty.


### <a id="open-closed-principle"></a>Open/closed Principle

The [open/closed principle](http://en.wikipedia.org/wiki/Open/closed_principle) states that _software entities should be open for extension, but closed for modification_. The ambiguity of this statement can be resolved through two variations of the principle. The variation due to Bertrand Meyer simply states that existing classes should only be modified in order to correct bugs. This restriction delivers the closed aspect of the principle. The open aspect is delivered through _implementation inheritance_, or in other words, inheritance with the goal of reuse rather than [subtyping](http://en.wikipedia.org/wiki/Subtype_polymorphism). The variation due to Robert C. Martin espouses openness through polymorphism which by definition also provides for closure since extensibility is supported through substitution rather than modification. Unfortunately, substitution often leads to accidental complexity, which must be addressed by yet another principle - the Liskov substitution principle discussed in the following section.

The primary utility of the open/closed principle is confinement of cascading changes while providing for extensibility. This is achieved by designing for extensibility and prohibiting changes to existing entities. Extensibility is attained by fancy tricks with abstract classes and virtual functions. Closure is attained by encapsulation, or rather by the hiding of moving parts. The existence of this principle merely exposes the object-oriented paradigm as a transitional introduction of polymorphism to imperative, state-oriented paradigms.

In a functional language, functions can be substituted at will and as such, there is no need to "design" for extensibility. Functionality requiring parametrization is naturally declared as such. Instead of inventing a concept of a virtual method and inheritance, one can rely on an existing, elementary concept - the [higher-order function](http://en.wikipedia.org/wiki/Higher-order_function).


### <a id="liskov-substitution-principle"></a>Liskov Substitution Principle

The [Liskov substitution principle](http://en.wikipedia.org/wiki/Liskov_substitution_principle) is essentially a restricted instance of subtyping which aims to guarantee semantic portability across class hierarchies. Portability is achieved by ensuring that whatever is true of a base type is also true of all subtypes. Subclasses must not strengthen preconditions - they must accept all input and initial state that the base class accepts and subclasses must not weaken postconditions - behavioral expectations declared by the base class must be met by the subclass. These characteristics cannot be enforced by the type system alone. The _is a_ relation of inheritance is thus deceptive - hence the need for a compensating principle. As such, the need for this principle demonstrates a pitfall in subtype (inclusion-based) polymorphism. Implicit factoring by class hierarchy imposes needless inclusion restrictions and requires complex principles o place a bound on accidental complexity.

Functional languages favor [parametric polymorphism](http://en.wikipedia.org/wiki/Parametric_polymorphism) with [bounded quantification](http://en.wikipedia.org/wiki/Bounded_quantification) thereby avoiding some of the pitfalls of inheritance. Informally, functional languages emphasize substitutability and deemphasize implementation reuse since reuse is better achieved through composition. Most ambitions of the Liskov substitution principle [are effectively trivial](http://apocalisp.wordpress.com/2010/10/06/liskov-substitution-principle-is-contravariance/) in a functional language.


### <a id="interface-segregation-principle"></a>Interface Segregation Principle

The interface segregation principle states that _no client should be forced to depend on methods it does not use_. In essence it is a restatement of the single-responsibility principle for interfaces and reflects the same underlying problem - the difficulty of balancing responsibility assignment, composition and encapsulation in object-oriented design. On the one hand, it is desirable to encapsulate, on the other hand it is desirable to compose. Furthermore, the problem with employing the interface-segregation principle alone is that it doesn't directly protect against class bloat and in some ways hides the problem.

Functional programming reduces the need for encapsulation by eschewing state and breeds composition at the core. There is no augmented concept of role-based interfaces because function roles are explicit at the onset. Functions are segregated by default.


### <a id="dependency-inversion-principle"></a>Dependency Inversion Principle

The dependency inversion principle states that high-level modules should be decoupled from low-level modules through abstractions. In other words, the principle states that code should be structured around the problem domain, and the domain should declare dependencies on required infrastructure as interfaces. Dependencies thus point inward to the domain model. The reason this principle is an _inversion_ is because typical architectures promoted by the object-oriented approach (via layer architecture) exhibit dependency graphs where high-level modules consume low-level modules directly. Initially, this dependency graph seems natural, since in expressing domain models in code one inevitably depends upon the constructs of the language. Procedural programming allows dependencies to be encapsulated by procedures. Subtype polymorphism defers procedure implementation. Unfortunately, use of subtype polymorphism (interfaces) is often overlooked for expressing domain dependencies in object-oriented implementations. Given that infrastructure code is typically more voluminous, the focus of the code drifts away from the domain. Domain-Driven Design was devised in part to balance this drift.

As a matter of course, the declarative and side-effect free nature of functional programming provide for dependency inversion. In object-oriented programming, high-level modules depend on infrastructure modules primarily to invoke side-effects. In functional programming, side-effects are more naturally triggered _in response_ to domain behavior as opposed to being _directly invoked by_ domain behavior. Thus dependencies become not merely inverted, but pushed to outer layers all together.



## <a id="domain-driven-design"></a>Domain-Driven Design

### <a id="aggregate"></a>Aggregate

The concept of the aggregate remains in functional programming, however it isn't expressed in terms of a class. Instead, it can be expressed as a quintuple, consisting of a set of aggregate states, an initial state, a set of commands, a set of events and a function mapping the set of commands onto the set of events given a state. Cohesion is provided by a module mechanism. The benefit of this formal definition is improved composition and reuse characteristics. A [functional F# implementation of a domain-driven design](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/InventoryItem.fs) illustrates this approach. There are no dependencies on persistence infrastructure and the same domain model can be used in an event-sourcing implementation, a key-value store as well as an ORM. Moreover, domain event side effects can be delegated to outer layers.

### <a id="immutable-value-objects"></a>Immutable value objects

Functional languages typically provide immutable record (product) and union (sum) types with structural equality which addresses this pattern trivially. Heavy reliance on state in object-oriented programming makes references or pointers a first class citizen rather than the structure of the data itself. Furthermore, the syntactical cost of declaring value object classes as well as difficulties in operating upon them can lead to [primitive obsession](http://c2.com/cgi/wiki?PrimitiveObsession).


### <a id="domain-events"></a>Domain Events

In a functional language, a domain event is simply a value returned by a function in an aggregate. Instead of relying on a [complex domain events infrastructure](http://www.udidahan.com/2009/06/14/domain-events-salvation/) in an object-oriented language, one can simply pattern-match on a value and invoke desired side-effects in response. This is illustrated by the [F# DDD example](https://github.com/eulerfx/DDDInventoryItemFSharp).

### <a id="intention-revealing-interface"></a>Intention-revealing interface

Since functional programming is more declarative, function names and interfaces tend to be more focused on intent instead of the the underlying mechanics. In addition, interfaces of side-effect-free functions are by nature more revealing because behavior is made explicit through the return type. In addition to a purely linguistic benefit of naming with intent, intent is also encoded by the type system.

### <a id="side-effect-free-functions"></a>Side-effect-free functions

Unlike imperative programming, functional programming makes side effects an explicitly designated exception - side-effect-free functions are the norm. This pattern is yet another example of how well crafted object-oritend design converges upon a functional style.

### <a id="assertions"></a>Assertions

Like many patterns rooted in imperative object-oriented design, assertions purport to wield implicit side-effects.

{% blockquote Eric Evans, Domain-Driven Design %}
When the side effects of operations are only defined implicitly by their implementation,
designs with a lot of delegation become a tangle of cause and effect. The only way to
understand a program is to trace execution through branching paths. The value of
encapsulation is lost. The necessity of tracing concrete execution defeats abstraction.
{% endblockquote %}

As with intention-revealing interfaces, assertions in functional languages are automatically encoded in the return type of a function in addition to the function name. In languages with powerful type systems such as F# and to a greater extent Scala, assertions often can be encoded by types directly making invalid states irrepresentable.

### <a id="conceptual-contours"></a>Conceptual Contours

Conceptual contours emerge when domain knowledge permeates the code to a sufficient degree. In object-oriented languages this can be achieved by carefully following principles of Domain-Driven Design. 

{% blockquote Eric Evans, Domain-Driven Design %}
When elements of a model or design are embedded in a monolithic construct, their
functionality gets duplicated. The external interface doesn't say everything a client
might care about. Their meaning is hard to understand, because different concepts are
mixed together.

On the other hand, breaking down classes and methods can pointlessly complicate the
client, forcing client objects to understand how tiny pieces fit together. Worse, a
concept can be lost completely. Half of a uranium atom is not uranium. And of course, it
isn't just grain size that counts, but just where the grain runs.
{% endblockquote %}

In functional languages, conceptual contours emerge more readily, once again due to the declarative and side-effect free nature of the paradigm. Specifically, clients of the domain model can rely on cohesive functionality attained with composition and yet still have access to constituents without breaking encapsulation.

### <a id="closure-of-operations"></a>Closure of operations

Closure of operations illustrates yet another example of coercing composition and structure upon object-oriented designs.

{% blockquote Eric Evans, Domain-Driven Design %}
Where it fits, define an operation whose return type is the same as the type of its
argument(s). If the implementer has state that is used in the computation, then the
implementer is effectively an argument of the operation, so the argument(s) and return
value should be of the same type as the implementer. Such an operation is closed
under the set of instances of that type. A closed operation provides a high-level
interface without introducing any dependency on other concepts.
{% endblockquote %}

Essentially, closure simplifies reasoning about a problem by restricting the domain of discourse. The [example of a functional implementation of a domain](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/InventoryItem.fs) exhibits this characteristic at a fundamental levels. The operation of applying a domain event is closed under the set of domain states. In terms of persistence, this naturally translates to event-sourcing but also supports persistence in a key-value store or ORM with no required modification.

### <a id="declarative-design"></a>Declarative Design

The overall intent of the aforementioned patterns is to cultivate a declarative design. As witnessed, functional programming is inherently more declarative and therefore more accommodating in this regard. Through declarative design, we can better distill distinguishing characteristics of the domain and reduce or eliminate coupling to orthogonal concerns of infrastructure. Consequently, re-usability, testability, correctness, maintainability and productivity qualities are tremendously enhanced.