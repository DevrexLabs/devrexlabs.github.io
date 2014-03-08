---
title: Introduction
layout: layout
---

## {{page.title}}


## What is OrigoDB?
OrigoDB is an in-memory database toolkit. The engine is 100% ACID, runs in-process and hosts
a user defined data model. The data model can be domain specific or generic and is defined using
plain old NET types. Persistence is achieved by snapshots and write-ahead command logging to the
underlying storage. Find out more on the [Introduction](/intro) page or the [Quick Start Guide](/docs/quick-start-guide).


This is a short introduction to OrigoDB to help you understand what it is which a lot of people have a problem with :)
First of all, the name OrigoDB implies database. We didn't call it a database at first

## Build faster systems, faster

Accessing data from RAM is orders of magnitude faster than reading from disk.
Also, data in RAM can be in a native format eliminating the need for object/relational
mapping or other translation, significantly reducing developer time and cost.

With OrigoDB, you write an in-memory data model, commands and queries using a NET language of your choice.
The engine is 100% ACID, taking care of persisting transactions and synchronizing access to the
in-memory model from multiple threads.

### Design goals
Our design goals focus around rapid development, testability, simplicity, correctness,
modularity, flexibility and extensibility. Performance was never a goal but OrigoDB easily 
outperforms disk oriented systems. That said, there is plenty of room for performance optimizations.

### Bring your own data model
Most database products build upon a single generic data model. Sql Server, Postgres, MariaDB et al use a relational data model.
With relational modelling you create a domain specific schema.

