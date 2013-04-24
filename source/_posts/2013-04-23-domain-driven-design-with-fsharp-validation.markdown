---
layout: post
title: "Domain-Driven Design (DDD) with F# - Validation"
date: 2013-04-23 09:38
comments: true
categories: [DDD,F#]
published: false
---
_In this post I dig deeper into validation of a Domain-Driven Design with F# and the functional paradigm. The implementation builds upon the [design introduced earlier](http://gorodinski.com/blog/2013/02/17/domain-driven-design-with-fsharp-and-eventstore/). The central theme is that of explicitness - the possibility of a failure is made explicit with type signatures. In turn, this calls for explicit handling of failure conditions paving the way for a design-by-contract style of programming. Under the hood, the implementation draws on principles of [category theory](https://en.wikipedia.org/wiki/Category_theory) which provide for uniformity and composability. As a result, from the perspective of DDD, the confluence of declarative design, supply design, invariants and assertions is tremendously enhanced. The implementation is to a great extent based on the work of [Mauricio Scheffer](https://github.com/mausch)._

<!--more-->

Validation can be a [tough topic](http://gorodinski.com/blog/2012/05/19/validation-in-domain-driven-design-ddd/). In modern object-oriented languages such as C# and Java, a notable reason for the difficulty is the impedance mismatch between exceptions, which are used to handle various types of error conditions, and methods which return normally. In addition to a complex implementation, usually requiring explicit support from the runtime, exceptions seem to almost invariably lead to bad programming practices. Contrary to its goal, the exception mechanism 

More specifically, the culprit is the exception mechanism itself.