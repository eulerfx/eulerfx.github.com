---
layout: post
title: "Object-oriented design patterns from a functional language perspective"
date: 2013-09-18 21:50
comments: true
categories: [OOP,functional]
---
_In this post I interpret design patterns and principles commonplace in [object-oriented programming](http://en.wikipedia.org/wiki/Object-oriented_programming) languages from the perspective of [functional programming](http://en.wikipedia.org/wiki/Functional_programming). Most of these patterns are trivially reduced to elementary functional constructs. The intent is to illustrate advantages and insight resulting from a shift in paradigm. After all, both object-oriented and functional paradigms have a shared goal - the solution of some problem. The patterns discussed herein are a testament to this commonality. The difference between the paradigms lies in the underlying abstractions upon which they are established which in turn have significant ramifications for the problem solving tactics they engender. In particular, I argue that the object-oriented inheritance model and the paradigm's primary utilization as an adorned state encapsulation mechanism are limiting factors in composing higher-level abstractions. Conversely, functional programming, being based on an entirely different [model of computation](http://en.wikipedia.org/wiki/Model_of_computation), eschews state, thereby bypassing accompanying hurdles and mandates composion from the core, thereby encouraging resuable abstractions._

## Disclaimers

The analysis of the object-oriented paradigm herein is based particularly on its manifestation in languages like C++, Java, C# and related languages. Functional programming examples are provided in F#.

This topic has been addressed priorly. Peter Norvig [discussed](http://www.norvig.com/design-patterns/) how most of the _GoF_ patterns are _invisible or simpler_ in LISP, calling the patterns _programming language bug reports_. Paul Graham [echoed](http://www.paulgraham.com/icad.html) the notion. Although both make use of a [dynamic](http://en.wikipedia.org/wiki/Type_system#Dynamic_type-checking_and_runtime_type_information) functional language, the arguments hold for statically typed functional languages. In fact, typing is orthogonal to the issue at hand.

## SOLID

### Single-Responsibility Principle

The single-responsibility principle states that every class should have a single responsibility where a responsibility is defined as a reason to change. The principle compensates for a typical OO anti-pattern of bloated classes. The syntactical cost of declaring a class compels programmers to marginalize, inevitably compromising composition.

In functional programming, the fundamental unit of abstraction is the function. Given that a function has a single input and a single output, they naturally have a single responsibility. One could certainly define arbitrarily generic function, though this would equate to swimming upstream. Moreover, functions are cheap to declare. When desired, functions can be factored with [equational reasoning](http://www.haskell.org/haskellwiki/Equational_reasoning_examples) having their behavior reconstructed with composition.

### Open/closed Principle

The open/closed principle states that _software entities should be open for extension, but closed for modification_. The primary utility of the principle is to subdue cascading changes while providing for extensibility. This is achieved by designing for extensibility and prohibiting changes to existing entities. Extensibility is attained by fancy tricks with abstract classes and virtual functions. Closure is attained by encapsulation, or rather by the hiding of moving parts. The existence of this principle merely exposes the the object-oriented paradigm as a transitional introduction of polymorphism to procedural, state-oriented paradigms. In a functional language, functions can be substituted at will

### Liskov Substitution Principle

The Liskov substitution principle is a restricted instance of subtyping which aims to guarantee semantic portability across the class hierarchy. Portability is achieved by ensuring that whatever is true of a base type is also true of all subtypes. Subclasses must not strengthen preconditions - they must accept all input and initial state that the base class accepts and subclasses must not weaken postconditions - behavioral expectations declared by the base class must be met by the subclass. These characteristics cannot be enforced by the type system which is why the principle is a set of guidelines. Consequently, the need for this principle demonstrates a pitfall in subtype polymorphism. Functional languages favor [parametric polymorphism](http://en.wikipedia.org/wiki/Parametric_polymorphism) with [bounded quantification](http://en.wikipedia.org/wiki/Bounded_quantification) thereby allowing programmers to avoid some of the pitfalls of inheritance. Indeed, the Liskov substitution principle [is essentially trivial](http://apocalisp.wordpress.com/2010/10/06/liskov-substitution-principle-is-contravariance/) in a functional language.


### Dependency Inversion Principle

The dependency inversion principle states that high-level modules should be decoupled from low-level modules through abstractions. The reason this is an _inversion_ is because it so happens that typical architectures promoted by the object-oriented approach promote an alternate configuration of dependencies where high-level modules consume low-level modules directly. The reason for this



## The Expression Problem