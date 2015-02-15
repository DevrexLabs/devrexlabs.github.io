---
title: Conference and user group presentations
layout: layout
comments: true
excerpt: Here are some of the talks we will giving at conferences during 2015
---

# {{page.title}}

Here are some of the talks we will giving at conferences during 2015. If you run a conference or user group and like what you see, please get in touch!

## The IMDB showdown - comparing OrigoDB, Redis ans Sql Server Hekaton
Moving data between RAM and disk is expensive both in terms of runtime performance but also developer productivity. A relational model, stored procedures, data access code and O/R mapping all add complexity and require significant time and effort to maintain.A solution based on a pure in-memory technology eliminates all or some of this extra work reducing both cost and time to market. Using a hybrid, such as Sql Server Hekaton, you can benefit from in-memory performance without sacrificing existing investments in existing RDBMS based architecture.

In this session, Robert introduces OrigoDB, Redis and Sql Server Hekaton, demonstrates an application based on each of them and discusses the pros and cons of each approach.

## OrigoDB extreme async makeover
By switching from a lock-based architecture to an asynchronous pipeline and persisting commands in batches, OrigoDB write performance increased from 3k to nearly 100k writes/second. Robert, the lead developer, presents and compares the four candidate solutions evaluated: TPL Dataflow, Akka.NET, Disruptor.NET and plain old TPL. And the winner is.... well, come and listen to find out!

In this session you will learn about the libraries and their patterns, get an insight into the internals of OrigoDB and some of the metrics used during the process. Readability and maintainability were important design goals so we will be looking at the code as well!

## Rocking DDD with OrigoDB
The subtitle of Eric Evans book "Domain Driven Design" is "Tackling complexity at the heart of software". Ironically, the event sourcing and CQRS based approaches to DDD are fairly complex on their own yet have become somewhat of a de facto standard. These approaches do have some merit, but there are simpler, often neglected, architectures worth considering before going full blown CQRS.

With OrigoDB all the domain objects live in-memory as a single object graph modelled with NET types and collections. Read and write transactions are served from the same in-memory model with full ACID guarantees. There is no need to explicitly deal with events for persistence or rehydrating aggregates, it's all taken care of behind the scenes.

In this session, Robert presents the Memory Image pattern (coined by Martin Fowler), OrigoDB and how it can be used to do DDD. We will also discuss pros and cons compared to an ES/CQRS.
