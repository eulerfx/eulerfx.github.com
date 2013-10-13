---
layout: post
title: "Object-oriented design patterns from a functional language perspective"
date: 2013-09-18 21:50
comments: true
categories: [OOP,FP]
---
_In this post I interpret design patterns and principles commonplace in [object-oriented programming](http://en.wikipedia.org/wiki/Object-oriented_programming) languages from the perspective of [functional programming](http://en.wikipedia.org/wiki/Functional_programming). Most of these patterns are trivially reduced to elementary functional constructs. The intent is to illustrate advantages and insight resulting from a shift in paradigm. After all, both object-oriented and functional paradigms have a shared goal - the solution of some problem. The patterns discussed herein are a testament to this commonality. The difference between the paradigms lies in the underlying abstractions upon which they are established which in turn have significant ramifications for the problem solving tactics they engender. In particular, I argue that the object-oriented inheritance model and the paradigm's primary utilization as an adorned state encapsulation mechanism are limiting factors in composing higher-level abstractions. Conversely, functional programming, being based on an entirely different [model of computation](http://en.wikipedia.org/wiki/Model_of_computation), eschews state, thereby bypassing accompanying hurdles and mandates composion from the core, thereby encouraging resuable abstractions._

## Introduction

The following claims are based on several few years of experience developing applications with the functional paradigm. In particular, porting code samples for Implementing Domain-Driven Design from a Java/C# implementation to an implementation in F#. 

## Disclaimers

The analysis of the object-oriented paradigm herein is based particularly on its manifestation in languages like Java, C# and related languages. Functional programming examples are provided in F#.

This topic has been addressed priorly. Peter Norvig [discussed](http://www.norvig.com/design-patterns/) how most of the _GoF_ patterns are _invisible or simpler_ in LISP, calling the patterns *"programming language bug reports"*. Paul Graham [echoes](http://www.paulgraham.com/icad.html) the notion. Similarly, Joe Armstrong describes [why OO sucks](http://harmful.cat-v.org/software/OO_programming/why_oo_sucks). Although both LISP and Erlang (designed by Armstrong) are dynamically type-checked, the arguments hold just as well for statically typed languages. In fact, the issue of typing is orthogonal.

Arguably, the power of abstraction in functional programming can be a double edged sword. For example, a [monoid](http://apocalisp.wordpress.com/2010/06/14/on-monoids/) is in essence a very simple abstraction - an interface consisting of two members. However, without some context, it can be troublesome to sense its value and utility. Abuse of such abstractions can lead to cryptic code, however this certainly isn't representative of the functional approach.

Finally, the intent herein is not to undermine the object-oriented paradigm. In many ways, well crafted object-oriented code converges upon a functional style. This is no surprise because as stated earlier, the eventual goals of both paradigms are one and the same.


## Objections

- Binding data structure to behavior is a mechanisms of state encapsulation - hiding the underlying problem.

- Lack of discriminated unions and pattern matching - optimize for encapsulation at the cost of composition resulting in a compositional "glass ceiling".

- Hierarchies are brittle. So much effort goes into making inheritance possible but it gives little in exchange. In fact, object-orientated patterns themselves favor composition over inheritance. But alas, OO languages only provide one tool for composition - a reference. Ultimately, in being a jack of two responsibilities, namely subtyping and reuse, it is a master of none and does a poor job with either.


## SOLID

### Single-Responsibility Principle

The [single-responsibility principle](http://en.wikipedia.org/wiki/Single_responsibility_principle) states that every class should have a single responsibility where a responsibility is defined as a reason to change. The principle compensates for the anti-pattern where bloated classes fulfill multiple roles. Classes can bloat for a few reasons. A core principle of object-oriented programming is the binding of data structure to behavior. The problem is that optimizing for data structure encapsulation not only weakens composition characteristics, but also hides the underlying problem of explicit state. As a result, object-oriented code typically contains many data structures with relatively few functions per. Adding methods to a class exerts pressure on the single-responsibility principle and reducing the number of methods can either make the data structure difficult to compose or all together pointless. Furthermore, the simple syntactical cost of declaring a class often compels programmers to marginalize. In my opinion, the importance of this purely mechanical issue must not be understated. A great deal of effort is put into optimizing development with IDEs and other tools, yet optimization can often be achieved at a far more fundamental level.

In functional programming, the fundamental unit of abstraction is the function. Given that a function has a single input and a single output, functions naturally have a single responsibility. One could certainly define arbitrarily generic function, though this would be counterintuitive. Moreover, functions are syntacticly thrifty.

// TODO: move to OCP
When desired and appropriate, functions can be factored with [equational reasoning](http://www.haskell.org/haskellwiki/Equational_reasoning_examples) having their behavior reconstructed with composition.


### Open/closed Principle

The [open/closed principle](http://en.wikipedia.org/wiki/Open/closed_principle) states that _software entities should be open for extension, but closed for modification_. The ambiguity of the principal statement can be resolved from the perspective of two variations. The variation due to Bertrand Meyer simply states that existing classes should only be modified in order to correct bugs. This restriction delivers the closed aspect of the principle. The open aspect is delivered through implementation inheritance, or in other words, inheritance with the goal of reuse rather than [subtyping](http://en.wikipedia.org/wiki/Subtype_polymorphism). The variation due to Robert C. Martin espouses openness through polymorphism which by definition also provides for closure since extensibility is supported through substitution. Unfortunately, substitution often leads to accidental complexity, which is addressed by yet another principle - the Liskov substitution principle.

The primary utility of the open/closed principle is to subdue cascading changes while providing for extensibility. This is achieved by designing for extensibility and prohibiting changes to existing entities. Extensibility is attained by fancy tricks with abstract classes and virtual functions. Closure is attained by encapsulation, or rather by the hiding of moving parts. The existence of this principle merely exposes the the object-oriented paradigm as a transitional introduction of polymorphism to imperative, state-oriented paradigms.

In a functional language, functions can be substituted at will and as such there is no need to "design" for extensibility. Functionality requiring parametrization is explicitly naturally declared as such. Instead of inventing a concept of a virtual method and inheritance, one can rely on an existing, elementary concept - the [higher-order function](http://en.wikipedia.org/wiki/Higher-order_function).


### Liskov Substitution Principle

The [Liskov substitution principle](http://en.wikipedia.org/wiki/Liskov_substitution_principle) is a restricted instance of subtyping which aims to guarantee semantic portability across class hierarchies. Portability is achieved by ensuring that whatever is true of a base type is also true of all subtypes. Subclasses must not strengthen preconditions - they must accept all input and initial state that the base class accepts and subclasses must not weaken postconditions - behavioral expectations declared by the base class must be met by the subclass. These characteristics cannot be enforced by the type system. The _is a_ relation is thus deceptive - hence the need for a balancing principle. As such, the need for this principle demonstrates a pitfall in subtype (inclusion-based) polymorphism. Implicit factoring by class hierarchy imposes needless inclusion restrictions and requires complex principles such as this to place a bound on accidental complexity.

Functional languages favor [parametric polymorphism](http://en.wikipedia.org/wiki/Parametric_polymorphism) with [bounded quantification](http://en.wikipedia.org/wiki/Bounded_quantification) thereby avoiding some of the pitfalls of inheritance. Informally, functional languages emphasize substitutability and deemphasize implementation reuse because reuse is better achieved through composition. Most ambitions of the Liskov substitution principle [are essentially trivial](http://apocalisp.wordpress.com/2010/10/06/liskov-substitution-principle-is-contravariance/) in a functional language.


### Interface Segregation Principle

The interface segregation principle is effectively a restatement of the single-responsibility principle for interfaces. It is a reflection of the same underlying problem - the difficulty of balancing responsibility assignment, composition and encapsulation. On the one hand, it is desirable to encapsulate, on the other hand it is desirable to compose. Functional programming reduces the need for encapsulation by eschewing state and breeds composition at the core.


### Dependency Inversion Principle

The dependency inversion principle states that high-level modules should be decoupled from low-level modules through abstractions. More abstractly, the principle states that code should be structured around the problem domain, and the domain should declare dependencies on required infrastructure as interfaces. Dependencies thus point inward to the domain model. The reason this principle is an _inversion_ is because typical architectures promoted by the object-oriented approach (via layer architecture) exhibit dependency topologies where high-level modules consume low-level modules directly. Initially, this dependency graph seems natural, since in expressing domain models in code one inevitably depends upon the constructs of the language. Procedural programming allows dependencies to be encapsulated by procedures. Subtype polymorphism defers procedure implementation. Unfortunately, use of subtype polymorphism (interfaces) is often overlooked for expressing domain dependencies in object-oriented implementations. Given that infrastructure code is typically more voluminous, the focus of the code drifts away from the domain. [Domain-Driven Design](http://en.wikipedia.org/wiki/Domain-driven_design) was devised in part to balance this drift.

As a matter of course, the declarative and side-effect free nature of functional programming provide for dependency inversion. In object-oriented programming, high-level modules depend on infrastructure modules primarily to invoke side-effects. In functional programming, side-effects are more naturally triggered _in response_ to domain behavior as opposed to direct invocation.



## Domain-Driven Design Tactical Patterns

### Aggregate

The concept of the aggregate remains in functional programming however it isn't expressed in terms of a class. Instead, it can be expressed as an quadruple, consisting of a set of states corresponding to the states of the aggregate at a given point, a set of commands, a set of events and a function mapping the set of commands onto the set of events given the state. Cohesion is provided by a module mechanism. The benefit of this formal definition is improved composition characteristics. A [functional F# implementation of a domain-driven design](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/InventoryItem.fs) illustrates this. There are no dependencies on persistence infrastructure and the same domain model can be used in an event-sourcing implementation, a key-value store as well as an ORM. Moreover, domain event side effects can be managed but outer layers.

### Declarative Design

Functional programming is by nature more declarative and therefore is more suitable for declarative design.

### Side-effect-free functions

Unlike imperative programming, functional programming make side effects an explicitly designated exception rather than the norm.

### Closure of operations

Closure of operations illustrates yet another attempt to emulate composition with object-oriented programming. 

### Immutable value objects

Functional languages typically provide immutable record (product) types with structural equality which addresses this pattern trivially. Heavy reliance on state in object-oriented programming makes references or pointers a first class citizen rather than the structure of the data itself.

### Supple Design

The guard rails provided by immutability coupled with sum types and pattern matching naturally support a supple design. A lack of supple design is indicative of failure to adhere to the single-responsibility principle and is usually driven by aversion to creating small, specialized classes. Ultimately this leads to [primitive obsession](http://c2.com/cgi/wiki?PrimitiveObsession).

### Intention-revealing interface

Since functional programming is mored declarative, function names and interfaces tend to be more focused on intent than the underlying mechanics.

### Assertions / Invariants

Like many patterns rooted in object-oriented design, assertions and invariants 

### Domain Events

In a functional language, a domain event is simply a value evaluated by a function in an aggregate. Instead of relying on a [complex domain events infrastructure](http://www.udidahan.com/2009/06/14/domain-events-salvation/) in an object-oriented language, one can simply pattern-match on a value and invoke desired side-effects in response. This is also illustrated by the [F# DDD example](https://github.com/eulerfx/DDDInventoryItemFSharp).

### Conceptual contours

Conceptual contours emerge when domain knowledge permeates the code to a sufficient degree. In object-oriented languages this can be achieved by carefully following principles of Domain-Driven Design. In functional languages, conceptual contours emerge naturally again due to the declarative and side-effect free nature of the paradigm.

{% blockquote %}
Sometimes people chop functionality fine to allow flexible combination. Sometimes they lump it
large to encapsulate complexity. Sometimes they seek a consistent granularity, making all classes
and operations to a similar scale. These are oversimplifications that don't work well as general
rules. But they are motivated by a basic set of problems.
{% endblockquote %}



## The Expression Problem

Is it really a problem? The advantage of the object-oriented approach is inconsequential.

http://lambda-the-ultimate.org/node/2232






- haskell/cartesian closed categories based architectures
- what's next? logic?