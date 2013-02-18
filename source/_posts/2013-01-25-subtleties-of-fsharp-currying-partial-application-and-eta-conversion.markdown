---
layout: post
title: "Subtleties of F# currying, partial application and eta-conversion"
date: 2013-01-25 20:55
comments: true
categories: [F#,FP]
---
F# is a functional programming language and as such provides various facilities for declaring, composing and transforming functions. Among these is [currying](http://en.wikipedia.org/wiki/Currying) which allows multi-variable functions to be regarded as chains of single-parameter functions each returning a function with one less variable that the last. Partial application allows fixing variables of a curried function returning a new function of smaller arity. Viewing functions in this way affords several advantages, including representation in the [lambda calculus](http://en.wikipedia.org/wiki/Lambda_calculus). An example in F# and a comparable C# implementation follow.

<!--more-->

{% gist 4640182 %}

and C#

{% gist 4640272 %}

The F# example contains a function *add* which is curried by default. The *add1* function is created via partial application on the *add* function. The *add1'* function has the same signature as *add1* and is declared without partial application but instead as direct invocation of *add*. In the C# example, the *Adder.Add1* function calls *Adder.Add* much like the *add1'* function in the F# example. The *CurriedAdder.Add1* is created in a manner more similar to F# by first curring *CurriedAdder.Add* with *CurriedAdder.Curry* and then fixing the first parameter.

The difference between *add1* and *add1'* is subtle. They have the same signatures and produce equivalent results. However, the declaration of *add1* states that it **is** *add* with the first parameter fixed. The declaration of *add1'* however states that it **calls** *add* with *1* as the first argument and its own argument as the second. In the scenario of these simple arithmetic functions, the difference is insignificant. If instead of using an integer literal a [impure function](http://en.wikipedia.org/wiki/Pure_function) call is made, behavior may change. For example:

{% gist 4662084 %}

Calling functions *addRand1* and *addRand2* will generate a new random number each time whereas in *addRand* the random value is effectively cached. The introduction of the variable *x* causes the invocation of the random function to be delayed and repeated. There are cases where this behavior is desirable, such as when encapsulating access to a cached data structure. For example:

{% gist 4662337 %}

Again, the signatures of *isPrime* and *isPrime2* are equivalent, except for presence of parenthesis in the first and explicit variable *x* in the second. The behavior is different however and in a scenario with larger sets it would be significant. 

The transformation from *isPrime* to *isPrime2* is called an [eta-abstraction](http://www.haskell.org/haskellwiki/Eta_conversion) in the lambda calculus. The inverse transformation is called an eta-reduction and the functions are said to be equivalent under eta-conversion. However, while functions are equivalent under eta-conversion, their behavior may be different in a subtle way. Beyond the examples given here, eta-abstractions can also be used to resolve [value restriction errors](http://blogs.msdn.com/b/mulambda/archive/2010/05/01/value-restriction-in-f.aspx) and to improve clarity of a [point-free expression](http://en.wikipedia.org/wiki/Tacit_programming).