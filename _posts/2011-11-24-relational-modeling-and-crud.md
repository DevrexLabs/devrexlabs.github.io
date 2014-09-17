---
title: Relational modeling and CRUD
layout: layout
comments: true
---

# {{page.title}}
I’m experimenting with support for relational modeling for #liveDB based on generics. Here’s a peek at how this is progressing.

## Entities

Entities of type E  are stored in an EntitySet<E>, which wraps a SortedSet<E>, a new collection class in .NET 4 implemented as a balanced binary tree with O(log N) read and write operations.  Entities need to be orderable within the sorted set, which can be achieved using a key property implementing IComparable or having the entity itself implement IComparable<E>.  Here are some example entities with keys defined in three different ways:

{% gist rofr/769653f48d2c7e26cb5f %}

The Category entity has a property named Id of type IComparable<int> which by naming convention will be used as a key. The User entity has a property marked with KeyAttribute. Task implements IComparable<Task> so Task objects will themselves be used as key and the Id property will be ignored. Task has a foreign key property, CategoryId,  but notice that none of the entities have any outbound object references. An entity can be an aggregation but must be an isolated from other entities meaning no inbound or outbound object references. For example, an Order entity can have a collection of OrderLine objects.

This isolation is important for queries. Imagine an N-to-N relationship between Category and Task above, where Category has a  collection of Tasks and Task has a collection of Categories. A query for a single task could possibly return every Task and Category in the database! This is a common beginners mistake. Normally, we deal with this issue by returning specific view objects stripped from references. We could for instance have a TaskView class with the categories represented as a list of string.

## The model
Next, create a model and derive from RelationalModel,  defining entity sets in the constructor:

{% gist rofr/f634e96864d2ff2be83e %}

Not much going on here, really.

## CRUD commands and querying
Now start your engine and you’re all set up for basic CRUD using commands of type AddEntityCommand<E>, RemoveEntityCommand<E> and ReplaceEntityCommand<E>. Of course, nothing is stopping you from also creating custom commands.  EntitySet<E> implements IEnumerable<E> so query as usual using lambdas or Query<M,R> instances.

Here is the AddEntityCommand<E> in action:

{% gist rofr/0fde983ec5d167ab4b0e %}

## Conclusion
Ok, so this isn’t really relational but it does enable basic CRUD, provided you do your modeling correctly. With CRUD you don’t have to write specific task-oriented commands and you can return entities from queries without having to map to reference-stripped view objects.