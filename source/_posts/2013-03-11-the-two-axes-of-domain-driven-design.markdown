---
layout: post
title: "The two sides of Domain-Driven Design (DDD)"
date: 2013-03-11 23:10
comments: true
categories: [DDD]
---
_This post explores Domain-Driven Design from the tactical and strategic perspectives with the goal of clearing confusion regarding its value and implications. Motivation sourced from [Outside In TDD versus DDD](http://blog.ploeh.dk/2013/03/04/outside-in-tdd-versus-ddd/) by [Mark Seemann](https://twitter.com/ploeh) where the author examines this misunderstanding surrounding DDD. _

It appears that many developers, myself included, initially attempt to harness Domain-Driven Design for the object-oriented programming patterns described by [Eric Evans](https://twitter.com/ericevans0) in [the Domain-Driven Design book](http://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215). This is no surprise because these patterns, which include concepts such as entities and repositories, are well founded, refined and hardened by years in service. Together, these patterns embody the tactical side of Domain-Driven Design. The other side of DDD consists of strategic principles and methodologies for analyzing and modeling domains as well as systematic techniques for catalyzing the development process. 

The most common misconception is that the tactical patterns _define_ DDD. In turn, this often leads to several problems. First, one can be lead to believe that if a design fails to adhere to tactical DDD patterns then it either isn't DDD or DDD can't be applied. For example, the [anemic domain model](http://martinfowler.com/bliki/AnemicDomainModel.html) anti-pattern is often utilized to demonstrate that a design is not a domain-driven design. From 

Second, and perhaps more importantly, one can overlook the very critical strategic patterns, such as bounded contexts, which can harm the rest of the design regardless of how well applied the tactical patterns are. I've experienced this fist hand where a project became increasingly difficult to iterate due to lack of model boundaries. All of the tactical patterns were there - the entities, repositories, aggregates, value objects. Instead of providing value however, these patterns become a burden. It became a burden to ensure that entities contained all the data and behaviors required to fulfill the needs of its consumers. It became a burden to ensure repositories scaled as the number of use-cases increased. The needed pattern was the bounded context.

- Fowler Domain Model
- Evan's video on what I've learned since the book
- Evan's mention that strategy should have come first
- Vaughn's book
- SRP, DIP, Law of Demeter, Information expert 
- History via Smalltalk 
- Functional DDD, event sourcing


