---
layout: post
title: "Toward persistence ignorance with NHibernate in Domain-Driven Design (DDD)"
date: 2012-08-10 12:35
comments: true
categories: [ddd,nhibernate]
---
Persistence ignorance is a quality of a design wherein entity classes are devoid of data access supporting characteristics. Adherence to this technique facilitates a separation between domain and infrastructure. By deflecting the technical noise, this separation streamlines the mapping between the model and its incarnation in code. In practice, persistence ignorance is an ideal seldom achieved in its entirety. Even if entities are stripped of all persistence related traits, the design tends to yield to influence from the persistence framework. Fortunately, like most things in programming, persistence ignorance falls upon a spectrum and can pay dividends even with partial application. After all, any type of code isn't an entirely noiseless medium.

<!--more-->

In domain-driven design, one way to attain persistence ignorance is with the use of a [repository](http://devlicio.us/blogs/casey/archive/2009/02/20/ddd-the-repository-pattern.aspx) and an [ORM](http://en.wikipedia.org/wiki/Object-relational_mapping). On the .NET Framework, the ORM [NHibernate](http://en.wikipedia.org/wiki/NHibernate) affords a relatively high degree of persistence ignorance with the use of out of band mapping declarations and reflection. What follows is a summary of various techniques for achieving persistence ignorance with NHibernate.

#### Default protected constructor

NHibernate requires that mapped classes provide a default (parameterless) constructor. This allows NHibernate to instantiate the mapped class during reconstitution. Although the footprint is relatively minor, this is an impure and unavoidable constraint for mapped entities. NHibernate could potentially employ a constructor injection technique, as utilized by [JSON.NET](http://james.newtonking.com/projects/json-net.aspx). If the default constructor is marked as protected then non-reflective client code cannot directly instantiate the class thus still restricting access with other constructors. An **ImplicitAttribute** could be created to mark protected constructors to prevent productivity tools from flagging them as unused.

#### Mapped member access strategies

NHibernate is capable of mapping database fields to object properties which have private setters. This is one of the most basic methods for maintaining persistence ignorance. Entities and value objects encapsulate domain concepts and as such should regulate access to their internal data. Private setters ensure that class data is only modified by the class itself thereby limiting scope and facilitating encapsulation. With use of [access and naming strategies](http://www.nhforge.org/doc/nh/en/#mapping-declaration-property), NHibernate can map to a backing field by convention. This can be useful when having a property setter invoked is undesirable during reconstitution, such as when the setter implements domain logic. If using [FluentNHibernate](http://www.fluentnhibernate.org/) for mapping, private fields having no corresponding property member can be referenced using the [**Reveal.Property<TEntity>**](https://github.com/jagregory/fluent-nhibernate/wiki/Mapping-private-properties) method.

#### Read-only collection mapping

A common pattern for implementing an aggregation relationship in DDD is with a collection coupled with a method which manipulates it. For example:

{% gist 3355179 %}

The methods **AddItem** and **RemoveItem** encapsulate access to the collection of line items in the order entity. The collection can be effortlessly mapped as a [one-to-many association](http://www.nhforge.org/doc/nh/en/#collections-onetomany). A problem with this approach is that encapsulation can be easily broken because the collection property is publicly exposed and nothing restricts calling code from accessing it directly. To resolve this problem, a backing field access strategy can be used to map the collection to a private field. The property can then return a [read-only wrapper](http://msdn.microsoft.com/en-us/library/ms132474.aspx) around the collection thereby isolating modification of the collection to class methods:

{% gist 3355222 %}

The methods now access the **items** field directly and attempts to modify the read-only collection returned by the **Items** property will throw a [NotSupportedException](http://msdn.microsoft.com/en-us/library/system.notsupportedexception.aspx).

#### Lazy loading with virtual members

By default, NHibernate requires class members to be virtual in order to support the [proxy pattern](http://en.wikipedia.org/wiki/Proxy_pattern); the proxy pattern in turn supports lazy loading. Lazy loading however [can be problematic](http://gorodinski.com/blog/2012/06/16/orm-lazy-loading-pitfalls/) and the requirement to make all members virtual is certainly a infraction against persistence ignorance, despite how negligible. Fittingly, disabling lazy loading at the class mapping level waives this requirement.

#### Custom mapping type

To support mapping requirements that cannot be fulfilled by direct field to property mappings NHibernate provides the [**IUserType**](http://www.martinwilley.com/net/code/nhibernate/usertype.html) and [**ICompositeUserType**](http://geekswithblogs.net/opiesblog/archive/2006/08/05/87218.aspx) interfaces. They enable implementors to declare arbitrary mappings between a set of fields and a component type or value type. Arbitrarily complex value types can be mapped using this technique and since the implementor controls construction the mapped types don't need to provide a parameterless constructor.

 






