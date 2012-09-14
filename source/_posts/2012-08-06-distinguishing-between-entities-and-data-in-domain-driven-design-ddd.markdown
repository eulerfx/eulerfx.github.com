---
layout: post
title: "Distinguishing between entities and data in Domain-Driven Design (DDD)"
date: 2012-08-06 22:16
comments: true
categories: [ddd]
published: true
---

In Domain-Driven Design, entities have a strictly defined life-cycle and identity. Accordingly, creation of entity instances should be restricted by a constructor with required parameters and possibly a factory. [Ideally](http://gorodinski.com/blog/2012/05/19/validation-in-domain-driven-design-ddd/), entities should not be allowed to enter an undefined state such as the state typically resulting from a parameterless constructor. There should be a small number of well defined places where new entity instances are created. Moreover, [as described by Udi Dahan](http://www.udidahan.com/2009/06/29/dont-create-aggregate-roots/), creation of aggregate roots can often be delegated to an existing entity in the context. These constraints simplify reasoning about code in addition to keeping focus on the domain and forcing one to carefully evaluate life-cycle related operations.

Entities aren't created *in* a vacuum nor are they created *from* a vacuum - normally there are data which are required for creation. For example, consider a simplified purchase order model:

{% img center http://yuml.me/a593c7c9 %}

In this case, **PurchaseOrder** is an aggregate root containing a collection of **Invoice** instances which themselves contain **LineItem** instances. In a line of business application, a purchase order would be created to represent a purchase of a set of products or services from a vendor. Subsequently, the vendor sends invoices which are associated with the purchase order. Since **PurchaseOrder** is an aggregate it manages the act of associating invoices with the purchase order. The association is performed by a method on **PurchaseOrder** called **AssociateInvoice** or simply **Invoice** since invoice can be a verb. A naive implementation could be: 

{% gist 3300767 %}

This method certainly gets the job done - it associates an invoice with a purchase order. Unfortunately, this implementation contains flaws and can be improved. For one, because the invoice instance is created outside of the purchase order aggregate, the **Invoice.PurchaseOrder** property must be publicly settable. This makes it difficult to determine whether a specific invoice instance is associated with a purchase order by looking at code alone. From a business perspective, an invoice is always associated with a purchase order and this constraint should be enforced in the code:

{% gist 3300796 %}

In order to support this implementation, the purchase order has to control the creation of the invoice which means that an **Invoice** object itself cannot be used as before. Instead, an **InvoiceData** object can be declared to contain the *data* representing an invoice. This object is elusively similar to the **Invoice** object, however it serves a different purpose. It allows the decoupling of the concept of data from the concept of entity thereby supporting the enforcement of domain constraints. Before association with a purchase order invoice data are just data in a vacuum and should therefore be represented as such. This entity data object pattern can be generalized to all cases where entity creation must be strictly controlled.

