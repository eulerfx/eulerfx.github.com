---
layout: post
title: "Json.NET Type Converters for the F# option, list and tuple types"
date: 2013-01-05 16:16
comments: true
categories: [f#, json.net, c#]
---

[Json.NET](http://json.codeplex.com/) is a popular [JSON](http://www.json.org/) serialization library on the .NET Framework. F# contains several types that aren't supported by Json.NET out of the box because they don't have direct analogs in C#. To enable support for these types in Json.NET a type provider implementation must be provided for each type. The types addressed in this post are [option](http://msdn.microsoft.com/en-us/library/dd233245.aspx), [list](http://msdn.microsoft.com/en-us/library/ee370372.aspx) and [tuple](http://msdn.microsoft.com/en-us/library/dd233200.aspx).

<!--more-->

## Option

The F# option is a type which explicitly represents the presence or absence of a value and is an [example of a monad](http://blogs.msdn.com/b/wesdyer/archive/2008/01/11/the-marvels-of-monads.aspx). It is a discriminated union declared as follows.

{% gist 4464415 %}

In C#, **Nullable<T>** is an analog for value types. For reference types, an absence of a value is normally represented with a **null** reference - [a billion dollar mistake](http://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare). Neither the **Nullable** type or **null** references however, can provide the elegance of the option type. 

In JSON, all values can be null, including .NET value types such as **Int32**. Therefore, an empty option can be represented in JSON with a **null**. The [JsonConverter](http://json.codeplex.com/SourceControl/changeset/view/69709#128133) implementation for the option follows.

{% gist 4464462 %}

The **CanConvert** method determines whether the converter supports a give type - an option in this case. The [typedefof&lt;T&gt;](http://msdn.microsoft.com/en-us/library/ee353533.aspx) construct returns the generic type definition of the type **T**. The **WriteJson** method serializes an instance of the supported type. In .NET, an empty option is a null reference and is serialized as such. Given the earlier observation that options are discriminated unions, the value of a non-empty option can be extracted using the [FSharpValue.GetUnionFields](http://msdn.microsoft.com/en-us/library/ee353849.aspx) helper method. The **ReadJson** method determines the type wrapped by the option and deserializes the value as usual. Next it creates an instance of an option value using the [FSharpValue.MakeUnion](http://msdn.microsoft.com/en-us/library/ee340309.aspx) helper method.

## List

An F# list represents an immutable series of values of the same type. It is implemented as a linked list and like the option type, it is a discriminated union declared roughly as follows.

{% gist 4464532 %}

This declaration states that a list is either empty or it is a tuple (pair) of a head, which is the first value in the list and a tail which is the remainder of the values in the list. The converter for the list type is defined as follows.

{% gist 4464543 %}

The list is serialized by first being converted to a **IEnumerable** which Json.NET already supports. Deserializing is achieved by first deserializing into an **IEnumerable** and then creating a suitable instance of the F# list type. Since a list is a recursive discriminated union, an instance of it is also created recursively with the inner **make** function which in turn calls **FSharpValue.MakeUnion**.


## Tuple

An F# tuple is a grouping of unnamed and potentially heterogeneous values. While an [analog](http://msdn.microsoft.com/en-us/library/system.tuple.aspx) exists in .NET version 4 and above, a custom converter implementation is still required. JSON does not provide direct support for tuples, but they can be represented in several ways. 

One way to represent a tuple in JSON is as an array so that the tuple **("hello",123)** would serialize to **["hello",123]**. This representation has the advantage that arrays already have serialization support and all that is required is the construction of a suitable tuple instance upon deserialization. F# provides the [FSharpValue.MakeTuple](http://msdn.microsoft.com/en-us/library/ee340410.aspx) helper method for creating tuple instances of a specified type given an array of objects denoting the values of the tuple. A first attempt might look like this:

{% gist 4464684 %}

Unfortunately, an object array deserialized in this way may not have the correct type for certain values, numeric values in particular. For instance, the tuple **("hello", 123)** is an instance of the tuple type **string** **\*** **Int32**. Upon deserialization however, the second element will have type [Int64](http://msdn.microsoft.com/en-us/library/system.int64.aspx) not [Int32](http://msdn.microsoft.com/en-us/library/system.int32.aspx). As a result, each array element must be deserialized individually into a type corresponding to the item in the tuple. This can be done as follows.

{% gist 4464710 %}

The [FSharpType.GetTupleElements](http://msdn.microsoft.com/en-us/library/ee353762.aspx) helper method returns an array of types stored by a tuple. The function **readElements** in the **ReadJson** method deserializes array elements individually thus ensuring an appropriate type.

Another way to serialize a tuple is to mirror the serialized shape of a [C# tuple](http://msdn.microsoft.com/en-us/library/system.tuple.aspx) so that the tuple **("hello", 123)** becomes **{"Item1":"hello","Item2":123}**. This representation is more explicit and will easily deserialize into C# tuples. A excellent account of serializing tuples in this format is depicted in [Getting Json.NET to Talk F#, Part 1: Tuples](http://pblasucci.wordpress.com/2011/06/16/getting-json-net-to-talk-f-part-1-tuples/).


## Summary

F# to C# inter-op isn't always a thing of beauty. However, the option, list and tuple types are ubiquitous in F# and thus JSON serialization support is essential, especially for enterprise applications. Given these converters, F# types can be used anywhere JSON serialization is required, such as [RavenDB](http://ravendb.net/) which was the motivating factor for these converters. The [RavenDB F# client](http://ravendb.net/docs/client-api/fsharp) provides generalized support for the union type with a [UnionTypeConverter](https://github.com/ravendb/ravendb/blob/master/Raven.Client.Lightweight.FSharp/Helpers.fs), however the converters in this post result in more idiomatic JSON representations.

## Source

The source code for this post can be found on [GitHub](https://github.com/eulerfx/JsonNet.FSharp).





