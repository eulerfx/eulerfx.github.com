---
layout: post
title: "The Domain-Driven Design (DDD) Maturity Model"
date: 2013-03-31 11:06
comments: true
categories: [DDD] 
---
_Adoption of Domain-Driven Design follows a pattern akin to [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) as expressed by the [Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html). Such a model, while not set in stone, can serve as a convenient anchor to gauge one's place on the path to DDD nirvana. Ultimately, the maturity model illustrates a more detailed analysis of Domain-Driven Design adoption beyond the [Two sides of DDD](http://gorodinski.com/blog/2013/03/11/the-two-sides-of-domain-driven-design/)._

<!--more-->

<div style="margin:0 auto;width:673px;height:541px;">
{% img /images/diagrams/ddd-maturity-model.png 673 541 Domain-Driven Design Maturity Model %}
</div>


### Level 0: Entities & Repositories
We begin by mapping domain concepts to classes. The notion of identity begins to formulate. Repositories emerge naturally as part of a data access layer. The resulting classes are normally data-centric containing few, if any, behaviors beyond [CRUD](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete). [ORM](http://en.wikipedia.org/wiki/Object-relational_mapping) frameworks come into play affording us a degree of persistence ignorance. Frequently, the complexity inherent in object-relational mapping displaces other concerns leaving many of us stranded at this level. Nevertheless, while we're not quite in DDD territory, the seeds of mapping the domain to a model expressed in code have been planted.

### Level 1: Supple Design
Once the fundamentals of entities and repositories are harnessed, we begin to refine the design. We draw upon design patterns and principles such as [SOLID](http://en.wikipedia.org/wiki/SOLID_\(object-oriented_design\)) and [GRASP](http://en.wikipedia.org/wiki/GRASP_\(object-oriented_design\)). The importance of value objects becomes more apparent. Notions such as side-effect free functions, closure of operations, and intention revealing interfaces begin to surface. We start to seek a more declarative design. As a result, the behavioral characteristics of the design begin to mature. Behaviors encapsulate changes to state and state is no longer haphazardly exposed. The ubiquitous language bears an increasing impetus. Refactoring toward deeper insight portrays the collaborative interplay between the code and the domain model.

### Level 2: Aggregates
As the model expressed in code becomes more supple and declarative we begin to face some of the limitations of modeling. A model should never strive to be an entirely genuine representation of reality, but it should be a useful one. As such, we begin to view aggregates as consistency boundaries as opposed to mere bags of methods and data in a loose correspondence with a real world entity. The notion of [eventual consistency](http://en.wikipedia.org/wiki/Eventual_consistency) emerges to encompass both a business intent as well as technical ramifications. Furthermore, we realize that technical constraints cannot be entirely deferred. We slowly mature at [Effective Aggregate Design](https://vaughnvernon.co/?p=139). Together, the patterns from this and previous levels formulate the tactical strategy of Domain-Driven Design.

### Level 3: Strategy
At the final stage of maturity we start to see Domain-Driven Design in the grand scheme of the software development process. We demarcate the model into bounded contexts which we in turn align with sub-domains. Development teams are in turn organized around bounded contexts. Context maps express relationships between bounded contexts. Relationships between teams are formalized. Attention is shifted to the core domain as the central business competency. We accept that complex software must be developed in collaboration. The ubiquitous language is distilled and shared amongst engineers, domain experts and stakeholders. Just as importantly, we develop an intimate understanding of the interplay between DDD and architecture.

## Interpretation
Just as the RMM does not intend to define REST, the DDD Maturity Model does not define DDD. However, this model can serve as a tractable guideline to evaluating DDD maturity. It is also important to emphasize the comprehension of the levels as a cohesive whole - one shouldn't pick and choose. The levels are intimately related and there is a strong forward-backward interaction between them. 


## Resources

- [Implementing Domain-Driven Design](http://www.amazon.com/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577)
- [Effective Aggregate Design](https://vaughnvernon.co/?p=139)
- [Domain-Driven Design: Tackling Complexity in the Heart of Software](http://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)