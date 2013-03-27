---
layout: post
title: "Distilling distinctions in the Ubiquitous Language in Domain-Driven Design"
date: 2013-03-22 10:50
comments: true
categories: [DDD]
---
_In this post I describe some of the subtleties involved in the formation of a ubiquitous language and domain modeling efforts in general. In particular, I fixate on the difficulties in making distinctions among concepts given a natural tendency to conflate terms and the implicit capacity to extract meaning from context._

<!--more-->

With a resounding lack of scientific evidence, I claim that a disproportionate fraction of modeling deficiencies that I have bestowed resulted from a failure to detect distinctions among concepts. It is as if I'm overly eager to combine ideas lest something is [repeated](http://en.wikipedia.org/wiki/Don't_repeat_yourself). The problem with this endeavor is quite simple - it results in incomplete, defective domain models. While the problem is simple, it may not be immediately obvious and can slowly but pervasively corrode the ubiquitous language. The ramifications resonate throughout the artifacts constituting a project, including the source code, documentation, reference scenarios, use cases, conversations with domain experts, etc.

## Homonyms

The tendency to conflate meanings of terms is inherent in natural language. [Martin Fowler](https://twitter.com/martinfowler) wrote about this and recently retreaded:

{% blockquote Martin Fowler http://martinfowler.com/bliki/TypeInstanceHomonym.html TypeInstanceHomonym %}
"War and Peace is a wonderful book.
"Let me see...pity this book has such a tattered cover"

In the first sentence 'book' refers to a literary work, one that's been with us for over a hundred years. In the second sentence 'book' now refers to a physical object that's probably been around for a lot less. 
{% endblockquote %}

In this example, the term 'book' has been conflated. The two meanings of the term are [homonyms](https://en.wikipedia.org/wiki/Homonym) - words that share spelling and pronunciation but have different meanings. The situation is further complicated by the fact that, while different, the meanings are very much related. Specifically, the physical book is an instance of the literary work. This is akin to the relationship between [objects](http://en.wikipedia.org/wiki/Object_\(computer_science\)) and [classes](http://en.wikipedia.org/wiki/Class_\(computer_science\)). In complex domains, distinctions such as these can be far more difficult to distill.

## Single Source of Truth & Reusability

Beyond the natural tendency to conflate is the pursuit of a [single source of truth](http://en.wikipedia.org/wiki/Single_Source_of_Truth). This pursuit is entirely justified because the introduction of a second source immediately defeats the purpose of truth. The notion of [reusability](http://en.wikipedia.org/wiki/Reusability) is another manifestation of this underlying principle. Reusability itself however is an end the means to which is [abstraction](http://gorodinski.com/blog/2012/05/31/abstractions/). And herein lies the problem - **under continuous abstraction, all things tend to becoming the same thing.** If we take away all that is physical about a book, it becomes the literary work it was originally an instance of. If we take away the characteristics which constitute a literary work we are left with nothing. The overarching challenge of the domain modeling process then, is maintaining a suitable degree of abstraction. This is much like the Federal Reserve controlling the money supply - too much results inflation, i.e. conflation, too little results in stagnation. 

Unsurprisingly, the dangers of abstraction aren't limited to domain modeling. [Rob Pike](https://twitter.com/rob_pike) stated, as part of the philosophy of the Go programming language - [dependency hygiene trumps code reuse](http://talks.golang.org/2012/splash.slide#28). Similarly, [Udi Dahan](https://twitter.com/UdiDahan) wrote about [The Fallacy of ReUse](http://www.udidahan.com/2009/06/07/the-fallacy-of-reuse/).

## Conflation Pitfalls

Modeling mistakes cannot be prevented, but they can be managed. In particular, I've found that it is easier to resolve improper distinctions among concepts than it is to resolve improper conflation among concepts. A interesting parallel to this principle is [integer factorization](http://en.wikipedia.org/wiki/Integer_factorization), the difficulty of which forms the basis of [public key cryptography](http://en.wikipedia.org/wiki/Public-key_cryptography), among other things. The important takeaway is that failing to detect distinctions can be dangerous. 

[Are you better at detecting differences between things or similarities between things?](https://twitter.com/eulerfx/status/310126313711206400)




