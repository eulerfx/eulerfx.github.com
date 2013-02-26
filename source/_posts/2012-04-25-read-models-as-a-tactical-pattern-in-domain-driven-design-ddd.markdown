---
layout: post
title: "Read-models as a tactical pattern in Domain-Driven Design (DDD)"
date: 2012-04-25 22:18
comments: true
categories: [DDD,architecture,C#]
---
Domain-Driven Design espouses the [repository pattern](http://domaindrivendesign.org/node/123) as a way to persist and reconstitute domain objects from a persistent store such as a database. Repositories are typically used to provide access to [aggregate](http://domaindrivendesign.org/node/88) roots, [entities](http://domaindrivendesign.org/node/109) and less frequently to [value objects](http://domaindrivendesign.org/node/135). Eric Evans attributes another potential responsibility to a repository in the blue book:

<!--more-->

{% blockquote Eric Evans, Domain-Driven Design %}
Although most queries return an object or a collection of objects, it also fits within the concept to return some types of summary calculations, such as an object count, or a sum of a numerical attribute that was intended by the model to be tallied.
{% endblockquote %}

This particular responsibility is not well explored or developed and as a result is often overlooked in practice. The basic premise of this approach is to leverage the underlying database, a relational database in particular, for what it's good at - namely aggregation, filtering, and projection. The requirement for this type of summarizing information depends on the project, but is commonplace in applications involving any type of GUI.

Consider the standard order model with an order entity containing a collection of line items. The order total is a sum of the constituent line item totals and the tax and shipping charges. In C# the corresponding classes might look something like this:

{% gist 2486326 %}

_Note that much of the behavior and constraint checking have been omitted for brevity._

This model appropriately represents the domain at hand. The only real behavior on this model is the calculation of the total. A typical application requirement calls for a view that displays a list of recent orders showing the order number, date and total. To support this, a repository contract might look like this:

{% gist 2486375 %}

From a client perspective the repository contract satisfies the requirements. However if implemented using an [ORM](http://en.wikipedia.org/wiki/Object-relational_mapping) such as NHibernate certain caveats arise even in this simplistic scenario. The relationship between an order and an order line item is one-to-many and in a relational model there would be a table for the orders and a table for the order line items related by a foreign key. When retrieving an order entity, the repository must also retrieve the order line items in order for the entity to be complete. This can be achieved using several fetching strategies, including joining the order line items table or issuing a second select statement to the database. For retrieving a single instance of an order entity this approach is acceptable however for collections of orders, as in the case of the GetMostRecent method on the order repository, this results in the [select N+1 problem](http://ayende.com/blog/1328/combating-the-select-n-1-problem-in-nhibernate). While NHibernate and other ORMs offer solutions to this problem, these solutions are relatively complex and can become a performance burden for both the database and the ORM. An alternative solution, one that is accordance with [KISS](http://en.wikipedia.org/wiki/KISS_principle), is to create an aggregating database query and map the results to simple, read-only objects:

{% gist 2495688 %}

One could also use the projection facilities provided by NHibernate. Regardless, the target class should match the shape of the query:

{% gist 2495699 %}

Note that this class contains no behavior and is read only. Instances of this class are meant for read-only purposes such as for display in a GUI. Accordingly, these types of classes are called read-models. Alternative names for these types of objects are reporting objects, views, projections and probably something else I'm not thinking of at the moment.

This trivial pattern boasts several advantages. The first of course is performance since relational databases are very adept at these types of queries. Next is simplicity - there is no simpler way of pulling data from a database - no ORM, no mapping magic, no problem! Furthermore, the developer isn't faced with coercing the order entity class to match the shape of the query at hand. __This is perhaps the central advantage from the DDD perspective - keeping the aggregates and entities pure and [Occam-esque](http://en.wikipedia.org/wiki/Occam's_razor).__ [Recent DDD guidance](http://dddcommunity.org/library/vernon_2011) suggests that aggregates should reference other aggregates using only the [identity](http://martinfowler.com/eaaCatalog/identityField.html) instead of direct association. This also simplifies the model and transitively the mappings. What if, however, a particular view requires the display of data contained in the associated aggregate? The aggregate itself can no longer be used but this is where the read-model pattern comes to the rescue. Conversely to the read-model, the write-model aggregate only need to be retrievable using its identity in which case an ORM is a suitable solution due to several convenient accommodations such as change tracking, generated SQL, concurrency control, etc. 

The read-model pattern can be compared to the notion of [consumer-driven contracts](http://martinfowler.com/articles/consumerDrivenContracts.html). Additionally, the read-model pattern can be viewed as a special case of [CQRS](http://martinfowler.com/bliki/CQRS.html) in that the model used for querying is different from the model used for processing commands. This is not full-fledged CQRS because read-models are pulled from the same data source as the write model. There is no requirement for a synchronization mechanism and the affiliated consistency and cache control concerns. However, the option remains to implement the synchronization mechanism at a later time when performance tests clamor for it. In CQRS, these read-models are usually called persistent read-models and are generated by event projections. The CQRS foreshadow also positions read-models as an intermediary refactoring step toward a CQRS implementation. For in depth treatment on CQRS look no further than [Rinat Abdullin](http://abdullin.com/).


