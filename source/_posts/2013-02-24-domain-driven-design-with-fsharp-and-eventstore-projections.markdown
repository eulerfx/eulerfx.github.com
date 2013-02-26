---
layout: post
title: "Domain-Driven Design (DDD) with F# and EventStore: Projections"
date: 2013-02-24 18:36
comments: true
categories: [F#,CQRS,DDD,EventStore,event sourcing]
---
_In this post I cover the introduction of simple projections into the [InventoryItem domain project](https://github.com/eulerfx/DDDInventoryItemFSharp) implemented with F# and [EventStore](http://geteventstore.com/)._

[Projections](http://cqrsguide.com/doc:projection) implement the query side of CQRS. Specifically in EventStore, projections are a mechanism for transforming event streams into other event streams. This has a wide range of applications including [CEP](http://en.wikipedia.org/wiki/Complex_event_processing). For this project, projections were used to generate read models, also known as views or query models.

<!--more-->

## Overview Projection

The [OverviewReadModelProjection.js](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/OverviewReadModelProjection.js) projection counts the total number of items in inventory. It does so by selecting events which change the count, namely *ItemsCheckedIn* and *ItemsRemoved*. Each occurrence of those events adjusts a state variable and emits a snapshot of the variable as an event. This event can then be retrieved as a read model to serve a query.

{% gist 5028145 %}

As described in the [projections series](http://geteventstore.com/blog/category/projections/) on the EventStore blog, the call to *emit* emits an event to an event stream with identity "InventoryItemOverviewReadModel". The *fromCategory()* function selects events in a specified category. Events are categorized by a built-in projection [$by_category](https://github.com/EventStore/EventStore/blob/master/src/EventStore/EventStore.Projections.Core/Standard/CategorizeEventsByStreamPath.cs) which determines the category from the stream ID. The object passed into the *when()* function contains functions for handling the desired events as well as a state initialization function. The first parameter of the event handling functions is a state variable maintained by EventStore. The state variable can be scoped at the projection level or per event stream, depending on how events are selected. In this case, the state variable is scoped at the projection level. The second variable is the event itself. It has the following structure:

{% gist 5028185 %}

The values contains the stream ID, event body, metadata, event type and other details. Note that this structure is dictated by the serialization format. As such, care must be take to ensure the structure is palatable both in streams, projections and code.

## Inventory Item State Projection

The [FlatReadModelProjection.js](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/FlatReadModelProjection.js) projection captures the state of individual inventory items including their name, count and active flag.

{% gist 5028201 %}

In this case, events are partitioned by stream with the *foreachStream()* function. As a result, the state variable will be a associated with each stream. The emitted events will be retrievable using the ID of the source aggregate stream and have category "InventoryItemFlatReadModel", for example "InventoryItemFlatReadModel-880852396f0f48c6b73d017333cb99ba".

## Reading

The projections are retrieved as read models using the [ReadStreamEventsBackward](https://github.com/EventStore/EventStore/blob/master/src/EventStore/EventStore.ClientAPI/EventStoreConnection.cs#L548) function to read the last event:

{% gist 5028226 %}

The read models are declared in F# as follows in the [ReadModels module](https://github.com/eulerfx/DDDInventoryItemFSharp/blob/master/DDDInventoryItemFSharp/ReadModels.fs):

{% gist 5028231 %}

Note that the structure of the read models has to match the structure of the events emitted by the projection.

## Conclusion

Projections in EventStore are a powerful mechanism with a wide array of applications. In this post, they were shown to support some basic read models. However, some scenarios can call for a document database or full-text search. In such cases events can be dispatched outside of the event store.

## Source

The source code for this post can be found on [GitHub](https://github.com/eulerfx/DDDInventoryItemFSharp).
