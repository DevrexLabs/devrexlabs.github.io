---
title: Speedment vs OrigoDB
layout: layout
comments: true
excerpt: Speedment is an exciting new startup in the java world leveraging in-memory in a way similar to OrigoDB. This article explains the Speedment concept and how it relates to OrigoDB.
---

# {{page.title}}

The other day I attended a meetup hosted by Speedment, an exciting start up leveraging the power of in-memory computing in a way similar to OrigoDB. It was a great evening that started off with free beer and pizza. Presentations by Hazelcast CTO Talip Ozturk and Speedment CTO Per-Åke Minborg followed. I made some new friends, got some new ideas and inspiration for future OrigoDB directions. Oh, and did I mention free beer and pizza?

In this blog post I'll present the Speedment core concept, why it's a very powerful idea and how you can achieve some of the same advantages using OrigoDB.  

Reading from memory is 6 orders of magnitude faster than random disk reads. Both Speedment and Origo benefit by holding large portions of data in optimized object graph structures in RAM. Hundreds of thousands or even millions of queries per second on a single commodity server is not uncommon. Whereas Origo takes an all-in approach, Speedment is architected to integrate with existing relational databases and legacy applications. A transactionally consistent subset of the relational data lives in RAM in the application process. After an initial pull population, database triggers are used to capture changes and keep the in-memory data in sync. Some of the strengths of this approach are:

* Entirely transparent to existing applications - just read and write the RDBMS as usual.
* Deliver immediate and measurable results incrementally and at a small cost- no need to rewrite the entire backend, just take it one query, one application at a time.
* Low risk compared to migration to other DB vendor - If things go bad just revert to the old code
* Improved RDBMS transaction throughput - By moving read load to the application, contention is reduced, allowing the rdbms to handle more write transactions.
* Query performance - querying the in-memory model is lightning fast
* Compile-time validation - Queries are expressed using Java code, including the new Java 8 Stream API
* Transactional consistency - The in-memory model is always in a consistent state. Compared to a traditional cache there are no race conditions or invalidation issues to worry about.

* image


Reads and writes at the application are managed by the Speedment Core. Writes are routed through the RDBMS and eventually propogate back to the application. Changes are fetched at a given interval and applied to the in-memory graph.

## Speedment UI
The Speedment UI is a graphical tool used to connect to the RDBMS and generate java code from the metadata. Entities, strongly typed collections, relationships, keys and indexes are mapped. It also generates DDL statements for the triggers and change log.

## The OrigoDB way
An OrigoDB model is usually hand-crafted and domain specific with no coupling to an existing relational model. Applying the Speedment concept we would monitor changes in an RDBMS using triggers (or the CDC feature of MSSQL) and update the in-memory model by mapping change events to OrigoDB commands.

* image 2

This is a fully viable approach but requires a deal of manual coding. Can we go all in with the Speedment way and generate an in-memory model from an existing RDBMS? Judging by the source, I think it would be fairly easy to integrate with the Speedment UI to generate C# code instead of java. An alternative which integrates nicely with Visual Studio, would be T4 template for use with the ADO.NET Entity Model.
