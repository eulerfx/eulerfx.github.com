---
layout: post
title: "Domain-Driven Design (DDD) with F# - Validation"
date: 2013-04-23 09:38
comments: true
categories: [DDD,F#]
---
_In this post I dig deeper into validation of a Domain-Driven Design with F# and the functional paradigm. The implementation builds upon the [design introduced earlier](http://gorodinski.com/blog/2013/02/17/domain-driven-design-with-fsharp-and-eventstore/). The central theme is that of explicitness - the possibility of a failure is made explicit with type signatures. In turn, this calls for explicit handling of failure conditions paving the way for a design-by-contract style of programming and equational reasoning. Under the hood, the implementation draws on basic principles of [category theory](https://en.wikipedia.org/wiki/Category_theory) which provide for uniformity and composability. As a result, from the perspective of DDD, the confluence of declarative design, supple design, invariants and assertions is tremendously enhanced. The functional implementation of validation is to a great extent based on the wonderful work of [Mauricio Scheffer](https://github.com/mausch)._

<!--more-->

## Pitfalls of Exceptions

Validation can be a [tough topic](http://gorodinski.com/blog/2012/05/19/validation-in-domain-driven-design-ddd/). In modern object-oriented languages such as C# and Java, a notable reason for the difficulty is the impedance mismatch between [exceptions](http://en.wikipedia.org/wiki/Exception_handling), which are used to handle various types of error conditions, and methods which return normally. The aftermath is a nonuniform interface coupled with reduced [autogenesis](http://en.wiktionary.org/wiki/autognosis). In addition to a [labyrinthine implementation](http://www.microsoft.com/msj/0197/Exception/Exception.aspx), usually requiring explicit support from the hosting runtime, exceptions seem to almost invariably lead to bad programming practices. Contrary to their ambition, exceptions compel programmers to defer and ultimately avoid explicit handling of errors. This is because exceptions are highly optimized for the non-degenerate case and despite guidelines and best practices, their fundamental flaws cannot be escaped.

The following is a typical example used to showcase exception deficiencies. 

{% gist 5457565 %}

The method interface is not uniform because the result can either be a integer value, which can be captured by assigning the result to a variable, or an exception which can only be captured by a catch block. Furthermore, static verification extends only to the non-exceptional case. In other words, the compiler will issue a warning if one tries to assign the result of this method to a string, but it won't issue a warning if the result is assigned to an integer variable. This is problematic because the integer type is a proper [subset](https://en.wikipedia.org/wiki/Subset) of the method's effective [range](http://en.wikipedia.org/wiki/Range_\(mathematics\)) - which is a union of the set of integers and an error case.

Unfortunately, the flaws of exceptions extend beyond interface irregularities to friction in designing exception hierarchies as evidenced by the likes of [ApplicationException](http://msdn.microsoft.com/en-us/library/system.applicationexception.aspx) in .NET. Moreover, it isn't immediately clear whether a given exception should ever be caught. In .NET, it is just as easy to catch an [ArgumentException](http://msdn.microsoft.com/en-us/library/system.argumentexception.aspx) as it is an [OutOfMemoryException](http://msdn.microsoft.com/en-us/library/system.outofmemoryexception.aspx) though it never makes sense to catch the latter since there is not way to handle it. If all of this wasn't enough, catching exceptions also incurs a performance penalty.

For more about the pitfalls of exceptions take a look at [Exceptions are Bad](http://blogs.atlassian.com/2011/05/exceptions_are_bad/).

## An Explicit Alternative

The functional programming paradigm addresses the afore-mentioned scenario with explicit typing. Since the range of the _Divide_ function is extended by the possibility of error, the range type is explicitly amplified. One such amplified type in F# is the [option type](http://msdn.microsoft.com/en-us/library/dd233245.aspx) use of which results in the following.

{% gist 5477487 %}

In this example, an erroneous result is explicitly encoded as an empty option. Therefore, the return type of the divide function is _Option&lt;int&gt;_ - an amplified integer. Note that in this case, the interface of the _Divide_ method is uniform because it is captured entirely in the return type. [Pattern matching](http://msdn.microsoft.com/en-us/library/dd547125.aspx) with the match clause allows the compiler to ensure that both result sub-types are handled explicitly. It follows that, unlike in the C# example, it unnatural to write functional F# code which defers handling of exceptional cases. One drawback of this technique however, is that it can become cumbersome to compose functions which return amplified types. We shall address this composition challenge in the following sections.

For a formal look at the origins of functional approaches to addressing exceptions and beyond take a look at [Tackling the awkward squad: monadic input/output, concurrency, exceptions, and foreign-language calls in Haskell](http://research.microsoft.com/en-us/um/people/simonpj/Papers/marktoberdorf/).

## Functional Validation in Domain-Driven Design

Let us return to the [original inventory domain](http://gorodinski.com/blog/2013/02/17/domain-driven-design-with-fsharp-and-eventstore/). In the original implementation, validation was performed in the function which executed an inventory item command:

{% gist 5477724 %}

In this example, a failed assertion will raise an exception which will bubble to an outer layer. While it certainly attains the desired result of preventing execution of the behavior, we will refine this implementation with explicit types.

The first order of business is finding an amplified type to encode both successful and erroneous results. While the _Option_ type, used in the integer division example above, captures both cases properly it doesn't provide any insight into why the result was erroneous. Fortunately, F# provides the [Choice type](http://msdn.microsoft.com/en-us/library/ee353439.aspx). Much like _Option_, _Choice_ is a [union type](http://msdn.microsoft.com/en-us/library/dd233226.aspx), the difference being that it also allows association of an arbitrary value with the second case. We shall use a two-case _Choice_ type to encode success with the first case and an error expressed as a list of string messages with the second case. Thus, the result type of the _exec_ function will be _Choice&lt;'TEvent, string list&gt;_ instead of just _'TEvent_. A basic implementation follows.

{% gist 5477784 %}

In this example, like the successful result, the erroneous result is returned explicitly. While this implementation avoids many of the pitfalls of exceptions, the syntax could certainly use some work. To address the syntax, we shall put the compositional facilities of F# to work. 

## A Functional Validation Framework

The validation framework presented here is based largely on the work of Mauricio Scheffer in [Validating with applicative functors in F#](http://bugsquash.blogspot.com/2011/08/validating-with-applicative-functors-in.html). This work has been refined and incorporated into [fsharpx](https://github.com/fsharp/fsharpx) drawing on powerful, category theory based composition mechanisms contained therein.

Initially, we define a primitive validator builder function.

{% gist 5477847 %}

This is a function which when given a predicate and an error value creates a single parameter function which passes on the parameter if the predicate returns true otherwise returning the error value. This function shall be used to compose more complex validators. 

One of the things we would like to do with these validating functions is write code which applies multiple validations on a parameter. If one of the validators fails, we would like to capture the error and continue collecting errors from subsequent validators finally returning a composite error result. If all validators succeed we pass on the parameter. Ultimately, the validation code should compose with code which executes behavior in the non-erroneous case.

[Applicative functors](http://en.wikibooks.org/wiki/Haskell/Applicative_Functors) will be used to attain the desired degree of composability. A [functor](http://learnyouahaskell.com/functors-applicative-functors-and-monoids) is a commonly used structure in Haskell and functional programming in general. It is sort of like a [monad](http://en.wikipedia.org/wiki/Monad_\(functional_programming\)) but weaker. For the C# programmer, a functor can be thought of as a set of extension method associated with an amplified type. For example, the [IEnumerable&lt;T&gt;](http://msdn.microsoft.com/en-us/library/9eekhta0.aspx) type, which amplifies _T_, together with the [Select](http://msdn.microsoft.com/en-us/library/system.linq.enumerable.select.aspx) extension method can be regarded as an instance of the functor [type class](http://en.wikipedia.org/wiki/Type_class). Intuitively, a functor provides a way to execute a function which operates upon the constituent type instead of the amplified type. 

An applicative functor extends the functor type class with additional functions called _pure_ and _apply_.  _Pure_ takes a non-amplified value and creates an instance of the amplified type corresponding to the functor. In the C# _IEnumerable_ case this would entail a function which when given a value returns an _IEnumerable_ containing that value - _yield return value;_. Apply for the _Choice_ amplified type is defined as follows.

{% gist 5477979 %}

The _apply_ function takes a function wrapped in a _Choice_ type and arbitrary value also wrapped in a choice type. It applies the function, if available, otherwise passing on the contained errors, possibly composing with errors from the arbitrary value. In effect, it handles each of the four cases that a set of two _Choice_ values can be in. In turn, we use these functions to compose the following functions.

{% gist 5478001 %}

The functions _lift2_, _&lt;?&gt;_, and _|?&gt;_ in this example form a sort of interface between instances of the _Choice_ type and non-amplified values.  The other two functions compose _Choice_ values together passing on the type of either the left or the right value. We can use these functions to refine the syntax in the inventory item example as follows.

{% gist 5478005 %}

In this example from the [InventoryItem](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/InventoryItem.fs) module, the _validName_ assertion, with the _&lt;*_ [operator](http://msdn.microsoft.com/en-us/library/dd233204.aspx), composes two validators from the [Validator](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/Validatior.fs) module which ensure that the inventory item name is neither null or empty. In the _exec_ function, the assertion is composed with the returned event using the _&lt;?&gt;_ operator. This operator takes a _Choice_ value on the left and a non-amplified value on the right and composes them into a single _Choice_ value the first case of which is a value of the type of the second operand. The order of the operands can be reversed with the _|?&gt;_ operator which can be read as [if and only if](http://en.wikipedia.org/wiki/If_and_only_if). Note the return type of the _exec_ function is _Choice&lt;Event, string list&gt;_.

## Refactoring Outer Layers

In the original example, the Aggregate module defined an aggregate as follows.

{% gist 4975076 %}

In order to incorporate the validation work herein, we change the signature of _exec_ to address erroneous results. The handler must also be changed to handle errors explicitly. The following snippet from the [Aggregate](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/Aggregate.fs) module provides an example.

{% gist 5478036 %}

In this example, only a successful executed command results in a commit to the event store. Otherwise, errors are propagated to the caller. The caller can in turn raise an exception or do anything else it may fancy. 

## Further Work

Beyond what was showcased in this post are deeper questions about explicit validation. How can we extract validation rules to outer layers such that they can be translated to client side JavaScript? Will a more descriptive error type be required? Can we use [F# computation expressions](http://msdn.microsoft.com/en-us/library/dd233182.aspx) to declare more complex validation workflows? 

## Summary

In this post we discussed the pitfalls of traditional validation techniques involving exceptions. Next, we implemented a validation mechanism which avoids exceptions and which as a result is in better alignment with functional programming. To this end, we were able to draw on basic concepts in category theory which studies composition among mathematical structures. Throughout our approach, the themes of uniformity, explicitness and declarative design prevail. The resulting code remains succinct, draws on static verification and provides better composition facilities. In particular, it provides for [equational reasoning](http://www.haskell.org/haskellwiki/Equational_reasoning_examples) which will be a topic of future posts. Additionally, the explicit implementation is simpler both in terms of readability, intuition and the requirements upon the runtime. Finally, the rich compositional facilities of F# allowed a solution that does not short-circuit like exceptions do, allowing clients to obtain all detected errors immediately.

## Source Code

The source for this post is on [GitHub](https://github.com/eulerfx/DDDInventoryItemFSharp).

