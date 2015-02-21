---
title: Conference and user group presentations
layout: layout
comments: true
excerpt: Here are some of the talks we will giving at conferences during 2015
---

# {{page.title}}

Here are some of the talks we have given/will be giving at conferences during 2015. If you run a conference or user group and like what you see, please get in touch!

* OrigoDB - take the red pill
* IMDB showdown - comparing OrigoDB, Redis and Sql Server Hekaton
* OrigoDB extreme async makeover
* DDD dead simple using OrigoDB
* OrigoDB - build faster systems faster (workshop)

## OrigoDB - take the red pill
OrigoDB is an in-memory database engine for Mono/NET providing full ACID support, lightning fast performance and offering a significant reduction of development and operational costs. No more relational modeling - use POCO’s and collections to define your domain model. No more stored procedures - write commands using your favorite .NET language and use LINQ for queries. No more data access code or object/relational mapping - just work with your in-memory domain model and let the OrigoDB engine transparently take care of persistence and consistency.

In this presentation you will learn what it is, how it works, how to use it and why it makes a difference.

## The IMDB showdown - comparing OrigoDB, Redis ans Sql Server Hekaton
Moving data between RAM and disk is expensive both in terms of runtime performance but also developer productivity. A relational model, stored procedures, data access code and O/R mapping all add complexity and require significant time and effort to maintain.A solution based on a pure in-memory technology eliminates all or some of this extra work reducing both cost and time to market. Using a hybrid, such as Sql Server Hekaton, you can benefit from in-memory performance without sacrificing existing investments in existing RDBMS based architecture.

In this session, Robert introduces OrigoDB, Redis and Sql Server Hekaton, demonstrates an application based on each of them and discusses the pros and cons of each approach.

## OrigoDB extreme async makeover
By switching from a lock-based architecture to an asynchronous pipeline and persisting commands in batches, OrigoDB write performance increased from 3k to nearly 100k writes/second. Robert, the lead developer, presents and compares the four candidate solutions evaluated: TPL Dataflow, Akka.NET, Disruptor.NET and plain old TPL. And the winner is.... well, come and listen to find out!

In this session you will learn about the libraries and their patterns, get an insight into the internals of OrigoDB and some of the metrics used during the process. Readability and maintainability were important design goals so we will be looking at the code as well!

## DDD dead simple using OrigoDB
The subtitle of Eric Evans book "Domain Driven Design" is "Tackling complexity at the heart of software". Ironically, the event sourcing and CQRS based approaches to DDD are fairly complex on their own yet have become somewhat of a de facto standard. These approaches do have some merit, but there are simpler, often neglected, architectures worth considering before going full blown CQRS.

With OrigoDB all the domain objects live in-memory as a single object graph modelled with NET types and collections. Read and write transactions are served from the same in-memory model with full ACID guarantees. There is no need to explicitly deal with events for persistence or rehydrating aggregates, it's all taken care of behind the scenes.

In this session, Robert presents the Memory Image pattern (coined by Martin Fowler), OrigoDB and how it can be used to do DDD.

## OrigoDB - build faster systems faster (workshop)
OrigoDB is an open source in-memory database toolkit for NET/Mono featuring ACID transactions and transparent persistence based on command sourcing and snapshots.

If the data fits in RAM, moving it back and forth between memory and disk is a waste of time and adds a lot of unnecessary complexity. With OrigoDB, you define a custom data model and transactions using your favorite CLR language, then just press play.

In this 4 hour fast-paced hands-on session you will learn all about OrigoDB from one of the core developers. Bring a laptop and code along or just sit back and enjoy the ride.

After a short introduction we'll dig into the coding basics including custom domain modeling, writing commands and queries, explicit vs. implicit transactions, unit testing, engine configuration and hosting.

We'll proceed with a look at some alternative modeling approaches, including immutable models with lock free concurrency and round off with an OrigoDB Server demo.
