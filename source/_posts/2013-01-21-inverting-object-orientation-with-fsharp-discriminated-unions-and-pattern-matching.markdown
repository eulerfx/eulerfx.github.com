---
layout: post
title: "Inverting object-orientation with F# discriminated unions and pattern matching"
date: 2013-01-21 21:41
comments: true
categories: [F#,OOP,FP]
---
_In this post I compare and contrast the use discriminated unions and pattern matching in F# to the object-oriented paradigm, particularly in C#. Additionally, aspects of the object-oriented approach are linked with GoF design patterns, multiple dispatch and the open-closed principle. Ultimately, discriminated unions are shown to address deficiencies in the imperative, object-oriented approach._

<!--more-->

One of the distinguishing types in F# as compared to most imperative object-oriented languages is the [discriminated union](http://msdn.microsoft.com/en-us/library/dd233226.aspx). A discriminated union is an [algebraic data type](http://en.wikipedia.org/wiki/Algebraic_data_type) ([F-algebra](http://en.wikipedia.org/wiki/F-algebra) for the mathematically inclined) which consists of a finite number of named cases which themselves can be of any type. A discriminated union value can be of one and only one case. In other words, a discriminated union is a _union_ because the set of cases forms a [union](http://en.wikipedia.org/wiki/Union_(set_theory\)). It is _discriminated_ because each case is distinguished from the others by its name. Many F# types including [Option](http://msdn.microsoft.com/en-us/library/dd233245.aspx) and [List](http://msdn.microsoft.com/en-us/library/dd233224.aspx) are defined as discriminated unions.

In C# a discriminated union can be represented as a class hierarchy where the base class corresponds to the union as a whole and sub-classes correspond to the cases. The ability to represent a discriminated union in this way may lead to a dismissive attitude in object-oriented developers. After all, mere brevity of representation, although important, is hardly a reason in and of itself. However, discriminated unions coupled with [pattern matching](http://msdn.microsoft.com/en-us/library/dd547125.aspx) take the discussion to a whole new level.

A central principle of object-oriented programming is the encapsulation of data with related behavior inside boundaries defined by a class. This complexity management mechanism manifest at all levels of a software system, from the smallest objects to entire applications. Objects are also intuitive because of their feigning affiliation with reality. There are however certain deficiencies to the object-oriented approach. The deficiency addressed in this post is based on the observation that classes optimize for extensibility through [inheritance](http://en.wikipedia.org/wiki/Inheritance_\(object-oriented_programming\)) thereby hiding inner structure. As a result, whenever behavior needs to depend on sub-classes of a class hierarchy it must either be placed directly into the sub-class or a derived class, or some hierarchy traversal mechanism must be employed. 

## The Visitor Pattern

Class hieararchy traversal can be implemented using several techniques. One such technique is the [visitor pattern](http://en.wikipedia.org/wiki/Visitor_pattern) the goal of which is to augment existing class structures with new behavior without modifying said structures. Consider for instance the [ExpressionVisitor](http://msdn.microsoft.com/en-us/library/system.linq.expressions.expressionvisitor.aspx) from the [System.Linq.Expressions](http://msdn.microsoft.com/en-us/library/bb506649.aspx) namespace. This class is used to traverse the structure of code represented as an [AST](http://en.wikipedia.org/wiki/Abstract_syntax_tree). Before this class was made public in the .NET Framework a similar implementation was used to implement LINQ support in NHibernate. This visitor pattern facilitates the [Open/closed principle](http://en.wikipedia.org/wiki/Open/closed_principle) by allowing objects, [Expression](http://msdn.microsoft.com/en-us/library/system.linq.expressions.expression.aspx) instances in this case, to remain _closed_ because they are not modified or inherited and _open_ in that new behaviors are added - translation to the Hibernate Query Language in this case.

The *ExpressionVisitor* operates by invoking an internal *Accept* method on an *Expression* instance. The *Expression* instance in turn effectively invokes a visit method on the visitor which corresponds to its specific type. The actual details are slightly more complicated, however the gist of the implementation follows:

{% gist 4601979 %}

To a large extent, the need for the visitor pattern is due to a lack of language support for [multiple dispatch](http://en.wikipedia.org/wiki/Multiple_dispatch). To understand multiple dispatch it is instructive to first consider [single dispatch](http://en.wikipedia.org/wiki/Single_dispatch). Single dispatch facilitates polymorphism in object-oriented languages by allowing method invocation to be based on the type of the instance which implements the method. Single dispatch is supported by most object-oriented languages including C# and Java. In multiple dispatch, the method invoked depends not only on the type of the instance, but also on the types of the arguments. The visitor pattern emulates multiple dispatch by employing single dispatch on the *Accept* method and then resolving the appropriate *Visit* method either with overloading or calling the method explicitly since the type of the visited object is handy.

The visitor pattern isn't the only way to implement multiple dispatch. In C# for example, one could invoke the [DLR](http://msdn.microsoft.com/en-us/library/dd233052.aspx):

{% gist 4602404 %}

This approach, while delegating the dispatch responsibilities to the runtime still suffers from a lack of static verification.


## Enter Discriminated Unions

The problem with the visitor pattern is that it is tedious to implement and there is no support from the type system to ensure that all sub-types are accounted for making it error prone. In F#, discriminated unions and pattern matching address both of these problems in an elegant way. The *Expression* and *ExpressionVisitor* types could be implemented in the following way:

{% gist 4602257 %}

The *Expression* is a recursively defined union type which corresponds to the class hierarchy of [Expressions](http://msdn.microsoft.com/en-us/library/system.linq.expressions.expression.aspx). The sample also makes use of the [F# tuple type](http://msdn.microsoft.com/en-us/library/dd233200.aspx). The *print* function accepts an expression value and unwraps it using pattern matching. This may initially seem like a glorified switch statement, however it is far more powerful. Most notably, the compiler ensures that all cases are handled. In this way, pattern matching turns any function accepting an expression into a statically checked "visitor".

Discriminated unions and pattern matching aren't limited to shaming the visitor pattern. They can also be used to implement the [state pattern](http://en.wikipedia.org/wiki/State_pattern), also in a statically verifiable fashion. A great example of this can be found in [Designing with types: Making state explicit](http://fsharpforfunandprofit.com/posts/designing-with-types-representing-states/). The resulting design not only prevents invalid state behaviors at runtime, it prevents them at compile time.

## Conclusion

We've witnessed the elegance with which discriminated unions address OOP deficiencies and lack of multiple dispatch. Discriminated unions and pattern matching allow data structures to be _inverted_ such that they are not only open for extension but extension is guided by support from the type system. Additionally, brevity of expression establishes the discriminated union as an effective modeling tool - after all, a language should be a place to organize one's thoughts. By contrast, the optimization for extensibility by inheritance in the object-oriented paradigm hides inner object structure making it more difficult to augment class hierarchies. Given the OOP adage of favoring composition over inheritance, this leaves much to be desired in existing object-oriented languages.


