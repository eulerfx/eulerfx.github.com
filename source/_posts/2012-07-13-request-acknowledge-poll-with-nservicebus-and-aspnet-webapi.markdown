---
layout: post
title: "Request/Acknowledge/Poll with ASP.NET WebAPI and NServiceBus"
date: 2012-07-13 17:26
comments: true
categories: [nservicebus,asp.net webapi,soa]
---

[Request/Acknowledge](http://servicedesignpatterns.com/ClientServiceInteractions/RequestAcknowledge) is a service design pattern wherein clients receive an acknowledgement as an immediate response while the original request is processed in the background. The acknowledgement typically contains a token for identifying the background task which can in turn be used to query the processing status of the task. This pattern is employed to reduce [temporal coupling](http://bit.ly/ricVKj) which is especially critical for requests requiring a long processing times. Instead of having the client wait for the final response a pull method for querying the status of the task or a push method for notifying the client is implemented. Similarly, the [event-based asynchronous pattern](http://msdn.microsoft.com/en-us/library/wewwczdw.aspx) in [OOP](http://en.wikipedia.org/wiki/Object-oriented_programming) shares the goal of reducing wasted wait time. Request/Acknowledge/Poll is a variation of this pattern wherein a method is provided for the client to query for the status of the task being processed. The other variation is Request/Acknowledge/Callback wherein a client is notified of task status immediately via callback mechanism. The callback variation ensures that the client receives task status information as it is generated but can be a burden to implement because the client must support the callback mechanism. Furthermore, it places the additional burden of tracking and invoking callbacks upon the server. The poll variation is simpler to implement and keeps the client in control of retrieving status information as it is needed.

The pattern consists of the following roles - the client, the service, the message queue, the request processor and the status repository. The client sends the initial request and polls the service for status information. The service receives the request, relays it to a message queue and returns an acknowledgement which typically contains a token to be used for identifying the task in subsequent status queries. The message queue serves to dismantle the temporal coupling between the sending of the request and receipt of the response. The request processor continuously dequeues messages from the queue and performs the actual processing of the request. The status repository is used to store information about the processing status of the task for retrieval by the client via the same service that accepted the initial request or by another service. Various frameworks can be utilized to implement the roles of the request/acknowledge/poll pattern and on the .NET platform a popular combination is [ASP.NET WebAPI](http://www.asp.net/web-api) for the service, [NServiceBus](http://nservicebus.com/) for messaging and the request processor and a dead simple implementation of the status repository backed by SQL Server or a plain old file system. 

** Example Domain **

A specific example based on a production system is in order. Consider the domain of an online retailer which sources products from various suppliers, optimizes the content and pricing and posts listings on sales channels. Product information is retrieved from the suppliers website, web service or a flat file. After import, product data is sanitized, optimized and merged with existing data. The import process is seeded by a list of product [SKUs](http://en.wikipedia.org/wiki/Stock-keeping_unit) provided by the supplier. As one can imagine, importing a product is a long running operation consisting of several steps and thus importing multiple products is also a long running operation. Furthermore, the import of multiple products bears a structure shared by processes where an identical operation is to be repeated for a set of items. The status of such processes typically contains the total number of items to be processed, the number of items processed so far as well as the number of errors.

** The service with ASP.NET WebAPI **

The service of the request/acknowledge pattern is trivial to implement because it serves a delegating role - a gateway between the client and the messaging infrastructure as well as the status repository. The ASP.NET WebAPI is an abstraction over [HTTP](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) which has specifications to fulfill the use case at hand - namely the [202 Accepted](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response status code as well as the [Location header](http://en.wikipedia.org/wiki/HTTP_location) or [Link header](http://www.w3.org/Protocols/9707-link-header.html). Status code 202 indicates to clients that "the request has been accepted for processing, but the processing has not been completed.". The location or link header can be used to specify the location of the resource representing the status of the process. 

{% gist 3157819 %}

In the sample, the import controller implements a controller resource which accepts an import request, generates a unique identifier for the task and creates an internal message to be sent with NServiceBus. Additionally, the service can handle authorization and validation. The model representing the import request, __ImportRequestModel__, is nearly identical to the internal command message, __ImportProducts__, however it is best to implement them as separate classes because they have slightly different roles and are handled by different frameworks with different constraints. The task status resource referenced in the location header can also be implemented with the WebAPI.

** The queue and request processor with NServiceBus **

An asynchronous message queue decouples the sender of a message from the receiver while ensuring message delivery despite downtime of either one. NServiceBus is an abstraction over an underlying messaging technology, typically [MSMQ](http://en.wikipedia.org/wiki/Microsoft_Message_Queuing). While there is a managed client for MSMQ, NServiceBus provides several industrial strength facilities beyond MSMQ that will help make the system production ready - namely [pub/sub](http://nservicebus.com/pubsub.aspx) and [sagas](http://nservicebus.com/Sagas.aspx). For the request/acknowledge pattern, NServiceBus serves as a framework for implementing the queue and request processor roles. The __IBus__ interface encapsulates the queue and a class implementing __IHandleMessages__ will be the request processor. A saga can be used to implement the processing "loop" for importing multiple products. This saga will handle the __ImportProducts__ message and send individual __ImportProduct__ messages to the appropriate endpoint. This endpoint, also implemented with NServiceBus, will handle individual __ImportProduct__ messages and publish an event upon completion or failure. The aforementioned coordinating saga will subscribe to these events in order to manage status information for the import task as a whole. The following is a sample implementation.

{% gist 3161028 %}

This saga implements the request processor with the method for handling the __ImportProducts__ message. Furthermore, the saga entity __ImportSagaData__ is used to track the processing status of the import task. Accordingly, this data is used to update the status of the task with the status repository. In this way, the saga coordinates the import task effectively implementing a distributed, fault tolerant processing loop. The saga data itself can be used instead of the status repository however the status repository is more convenient for a few reasons. First, the saga method __MarkAsComplete__ deletes the saga data entity making the status unaccessible after the task complete. While it is possible to omit the call to the method, leaving it serves as documentation. Second, the saga data is persisted with the __ISagaPersister__ which creates a dependency on the specific implementation. Third, the saga entity isn't accessible until the initial message completes processing and the entity is persisted. Depending on the size of the task, there may be a number of products that have already been imported at that point without the status being updated.

Utilization of a durable messaging technology, such as MSMQ, makes the system resilient to crashes and downtime all while normalizing the load curve. The endpoint which handles individual __ImportProduct__ messages can go down in the middle of an import and pick up where it left off once it is back up. Furthermore, with the use of the NServiceBus [distributor](http://nservicebus.com/Distributor.aspx), processing can be scaled out transparently. A caveat in implementing the coordinating saga with NServiceBus v2.6 and below is that the default NHibernate based saga persister [isn't thread safe](http://tech.groups.yahoo.com/group/nservicebus/message/12975) which means that the endpoint can only have a single worker thread. Given that the work performed by this saga is relatively lightweight this isn't usually an issue.

** The status repository **

The status repository can be implemented in a variety of ways and is hardly worth a mention. As a tangential aside, like the ones so forever tantalizing for software engineers, [Redis](http://redis.io/) presents an attractive solution for the status repository because of its data structural commands such as [INCR](http://redis.io/commands/INCR). The status repository interface used in the example follows.

{% gist 3161060 %}

** Summary **

As demonstrated, it is straightforward to implement the request/acknowledge pattern with the ASP.NET WebAPI and NServiceBus. This pattern allows clients to invoke long running operations without having to be bound to the processing time. The service role is implemented with ASP.NET WebAPI. The queue and request processor roles are implemented with an NServiceBus saga, which also coordinates the processing loop. Beyond the elimination of temporal coupling the described system is resilient and horizontally scalable. Despite their conveniences, we aren't bound to any particular framework or platform - there exist a plethora of messaging technologies and web service frameworks that can fulfill the required roles. For reference, queuing can be supported by [MassTransit](http://masstransit-project.com/) over [RabbitMQ](http://www.rabbitmq.com/) or [ActiveMQ](http://activemq.apache.org/). [JAX](http://en.wikipedia.org/wiki/Java_API_for_XML_Web_Services) on the Java platform can be used in place of ASP.NET WebAPI. Most importantly, one must understand the role each technology plays and envision the system as being composed with these roles. Care must be taken to ensure that the system isn't deeply locked into any particular vendor.

** Resources **

- [Service Design Patterns: Fundamental Design Solutions for SOAP/WSDL and RESTful Web Services](http://www.amazon.com/Service-Design-Patterns-Fundamental-Solutions/dp/032154420X)
- [RESTful Web Services Cookbook: Solutions for Improving Scalability and Simplicity](http://www.amazon.com/RESTful-Web-Services-Cookbook-Scalability/dp/0596801688/)
- [SAGAS](http://www.cs.cornell.edu/andru/cs711/2002fa/reading/sagas.pdf)





  








