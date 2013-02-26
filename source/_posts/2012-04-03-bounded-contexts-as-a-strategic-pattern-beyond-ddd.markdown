---
layout: post
title: "Bounded Contexts as a strategic pattern beyond DDD"
date: 2012-04-03 23:15
comments: true
categories: [DDD,SOA,architecture]
---
The term bounded context (BC) was introduced by Eric Evans in the Domain-Driven Design ["blue book"](http://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215/ref=sr_1_1?ie=UTF8&qid=1332969996&sr=8-1) Part IV *Strategic Design*, 
Chapter 14 *Maintaining Model Integrity*.

<!--more-->

## Defining Bounded Contexts

A definition from the book:

{% blockquote Eric Evans, Domain-Driven Design %}
A BOUNDED CONTEXT delimits the applicability of a particular model so that team members have a clear and shared understanding of 
what has to be consistent and how it relates to other CONTEXTS. Within that CONTEXT, work to keep the model logically unified, 
but do not worry about applicability outside those bounds. In other CONTEXTS, other models apply, with differences in terminology, 
in concepts and rules, and in dialects of the UBIQUITOUS LANGUAGE.
{% endblockquote %}

A domain model is thus valid only within a context and may not and more importantly should not be applicable to another context. All too often I and many other developers fall into the trap of combining disparate contexts. What follows are attempts to coerce a single model to fit requirements of disparate contexts. This comes at no surprise given an inherent tendency to follow the [DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself) principle. This principle has value and the value is immediately evident in the most basic factoring of code, such as extracting a set of instructions into a sub-routine. DRY however is very much a double edged sword and can lead one astray when applied incorrectly. As easy as it is to gather value from this principle is to apply it incorrectly. Incorrect application often occurs when subtle nuances in the model are ignored, when responsibilities are conflated. Indeed, it is the proper division of responsibilities that leads to breakthroughs in the model. Furthermore, in practice, it is often easier and more rewarding to unify different parts of the model than it is to divide one part into two. For an in-depth discussion on the pitfalls of reuse, take a look at [The Fallacy of Reuse](http://www.udidahan.com/2009/06/07/the-fallacy-of-reuse/) by Udi Dahan. 

Bounded contexts provide encapsulation much like methods and objects do, but at a higher architectural level and should therefore be a principal top-level architectural concern. [Structured programming](http://en.wikipedia.org/wiki/Structured_programming) provided basic abstraction mechanisms to assist in organizing code and reasoning about code. [Object orientation](http://en.wikipedia.org/wiki/Object-oriented_programming) continued this paradigm by allowing the packaging of code and data into objects. Similarly, the [UNIX philosophy](http://en.wikipedia.org/wiki/Unix_philosophy) emphasized small and well defined modules within a interconnected ecosystem. [SOA](http://en.wikipedia.org/wiki/Service-oriented_architecture) purported to address the need for even higher levels of organization with services. The unifying theme is that of encapsulation and bounded contexts are no different.

A distinguishing characteristic of bounded contexts, however, is their alignment with the domain instead of a technical axis. And this is quite becoming since all code is designed to solve problems in some domain. Thus bounded contexts emerge as a high level abstraction mechanism. Bounded contexts are not artifacts of programming languages or frameworks, they are artifacts of the very essence of how humans think. Indeed part of the SOA vision was alignment between business and IT. However, this was somehow lost amidst an overzealous fixation on the technology, the XSD schemas, the governance guidelines. Bounded contexts and Domain-Driven design shift the focus back on the domain.

The importance of isolating appropriate bounded contexts in a domain cannot be overstated. Domain boundaries will determine characteristics of the model, but also delimit consistency boundaries and govern solution/project structure in the IDE. Few developers will argue against smaller and more focused solutions, projects, and classes. 

## Designing Bounded Contexts

To shamelessly re-quote [SICP](http://mitpress.mit.edu/sicp/full-text/book/book.html):

{% blockquote John Locke, An Essay Concerning Human Understanding (1690) %}
The acts of the mind, wherein it exerts its power over simple ideas, are chiefly these three: 1. Combining several simple ideas into one compound one, and thus all complex ideas are made. 2. The second is bringing two ideas, whether simple or complex, together, and setting them by one another so as to take a view of them at once, without uniting them into one, by which it gets all its ideas of relations. 3. The third is separating them from all other ideas that accompany them in their real existence: this is called abstraction, and thus all its general ideas are made.
{% endblockquote %}

Proper delineation of boundaries is not an exact science and follows an organic progression through iterations and continuous integration just as the rest of the development process. Developers can apply [GRASP](http://en.wikipedia.org/wiki/GRASP_\(object-oriented_design\)) guidelines to the design of bounded contexts, especially concepts of low coupling, high cohesion and the information expert pattern. The [dependency-inversion principle](http://en.wikipedia.org/wiki/Dependency_inversion_principle) states *Abstractions should not depend upon details. Details should depend upon abstractions.*. This is yet another variation on the central theme of aligning code artifacts around the domain.

Unfortunately, these guidelines are excessively declarative and provide little insight into the how. Much like the mathematical statement that the square root of 2 is a number wich when squared equals 2 provides little insight into [Newton's method](http://en.wikipedia.org/wiki/Newton's_method). Therefore, the structure of a bounded context must be distilled from interactions with subject matter experts, from refinement of the ubiquitous language and a process of continuous adaptation. From a software engineering perspective, developers and architects should also take care to make the cost of creating new bounded contexts as low as possible to prevent friction. Friction can cause teams to avoid bounded contexts in the first place.

## Summary

The bounded context is a strategic pattern that was originally devised for Domain-Driven Design but is applicable regardless of methodology and technology. It maps to the natural human instinct to partition structures into composites and should be the cornerstone of the modeling and design phase. Bounded contexts make business and IT alignment explicit and relieved of technical concerns. In this way, they reinvigorate the original SOA vision. However, patterns for designing bounded contexts are still in their infancy.

## Resources

- [This InfoQ article](http://www.infoq.com/articles/ddd-contextmapping) provides a great example of bounded contexts and context mapping.
- [Vaughn Vernon's](https://twitter.com/vaughnvernon) upcoming book strives to place initial focus on bounded contexts and the rest of the strategic DDD patterns which will make a great addition to the existing knowledge base.
- [Rinat Abdullin's](https://twitter.com/abdullin) [_Anatomy of Distributed System a la Lokad_](http://abdullin.com/journal/2012/3/31/anatomy-of-distributed-system-a-la-lokad.html) article expounds on bounded contexts as a modelling tool.
- [Martin Fowler's](http://martinfowler.com/) vintage [_Analysis Patterns_](http://www.amazon.com/Analysis-Patterns-Reusable-Object-Models/dp/0201895420) is a wonderful source of inspiration for analyzing domains.
- In [this InfoQ interview](http://www.infoq.com/interviews/greg-young-ddd) [Greg Young](https://twitter.com/gregyoung) emphasizes the importance of bounded contexts.



