---
layout: post
title: "Sub-domains and bounded contexts in Domain-Driven Design (DDD)"
date: 2013-04-29 21:15
comments: true
categories: [DDD]
---
_In this post I discuss the subtle relationship between bounded contexts and sub-domains within strategic Domain-Driven Design. The concepts often seem elusively similar, yet in reality are more like parallels. To understand the parallels, the association between a domain of discourse and the corresponding model shall be investigated. My understanding of the subject matter was honed at [DDD Denver Lean Coffee Discussions](http://vimeo.com/63635620) organized by [Paul Rayner](http://www.virtual-genius.com/)._

<!--more-->

## Domain Models

The key to understanding the difference between a bounded context and a sub-domain is understanding the difference between a domain and a domain model. The domain, in effect, is the problem to be addressed with a software effort. It is the problem space. A domain can be decomposed into sub-domains which typically reflect some organizational structure. A common example of a sub-domain is Product Catalog or Accounting - the latter possibly a generic sub-domain. A domain model on the other hand is an abstraction of a domain taking whats necessary to satisfy requirements. It has to be created with the cooperation of developers and domain experts. If the design and analysis process was a mathematical function, the model would be its [range](http://en.wikipedia.org/wiki/Range_\(mathematics\)). In fact, it would have to be a [non-injective and non-surjective](http://en.wikipedia.org/wiki/Bijection,_injection_and_surjection) function:

{% blockquote George E. P. Box, Empirical Model-Building and Response Surfaces (1987) %}
Essentially, all models are wrong, but some are useful.
{% endblockquote %}

## Bounded Contexts

While sub-domains delimit the applicability of domains, bounded contexts delimit the applicability of domain models. As such, the bounded context is within the solution space. The relationship between sub-domains and bounded contexts however is deeper still. The goal of a domain driven design is an alignment between the domain and the software. Ideally, there would be full alignment between a sub-domain and a corresponding bounded context. For example, there would be an accounting bounded context that expresses the model of an accounting sub-domain. An alignment between a sub-domain and a bounded context facilitates reasoning within both the solution space and the problem space. 

Reality however isn't always so forgiving. Given that bounded contexts are a software artifact, there is no fundamental force of nature which would firmly bind it to a domain. This is especially true for legacy software developed without a domain-driven approach. This is also true of integrations with 3rd party software which was developed by a different team with a different perspective on a model which is unlikely to be [isomorphic](http://en.wikipedia.org/wiki/Isomorphism) to the model at hand. Furthermore, a bounded context can be somewhat technical in nature. For example, a product catalog usually requires a full-text search system which can form a bounded context in its own right. Even though the sub-domain is a product catalog, there are at least two bounded contexts implementing it. Given these constraints, we must accept a degree of elasticity in the association between sub-domains and bounded contexts.

## Conway's Law

As noted by Paul Rayner at the Lean Coffee Discussion, it is instructive to contemplate the nature of sub-domain boundaries. How are those boundaries determined in the first place? [Conway's Law](http://en.wikipedia.org/wiki/Conway's_law) states:

{% blockquote Melvin Conway, How do Committees Invent? (1968) %}
Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations.
{% endblockquote %}

According to Conway's Law, sub-domain boundaries are determined in part by communication structures within an organization. This is often a acceptable demarcation since communication structures have likely been tested and refined over time. From an business perspective, the formation of an organization is driven by a compulsion to reduce transaction costs. From an abstract systems perspective, communication structures strive to reduce coupling by increasing cohesion. These and the coupling requirements among bounded contexts are all shadows of a single principle cast in different directions.

## Model Exploration Whirlpool

Much like different models can be designed for a domain, a domain can itself be interpreted in different ways. Moreover, there is no canonical interpretation and the interpretation must be continuously evolved based to a great extent on feedback from the domain model. Eric Evans refers to this process as the [model exploration whirlpool](http://www.domainlanguage.com/ddd/whirlpool/Domain_Language_Model_Exploration_Whirlpool_v2010-06-19.pdf). 

## Summary

A sub-domain delimits a domain and exists within the problem space. A bounded context delimits the _domain model_ and exists within the solution space. The ideal is full alignment between a sub-domain and a bounded context, however in practice a degree of flexibility must be accepted in this regard. Furthermore, just as the bounded context is guided by a sub-domain, a sub-domain is, in turn ,guided by a bounded context as part of the model exploration whirlpool.

<div style="margin:0 auto;width:500px;height:418px;">
{% img center /images/posts/drawing-hands.jpg 500 418 'MC Escher Drawing Hands' %}
</div>

## Resources

- [Domain-Driven Design: Tackling Complexity in the Heart of Software](http://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215) _Eric Evans_
- [Implementing Domain-Driven Design](http://www.amazon.com/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577) _Vaughn Vernon_
- [DDD Denver Lean Coffee Discussion](http://vimeo.com/63635620)
