---
title: Introduction
layout: submenu
---

## What is OrigoDB?
OrigoDB is an in-memory data platform with an ACID transactional engine at it's heart. OrigoDB is maintained by Devrex Labs, a software development, consulting and training firm based in Sweden. The core engine, plugins, data models, examples and a workshop are all [open source and hosted on github](http://github.com/devrexlabs). OrigoDB Server is a commercial offering with enterprise features.

## Design goals
Our initial design goals were focused on rapid development, testability, simplicity, correctness, modularity, flexibility and extensibility. Performance was never a goal but running in-memory with memory optimized data structures outperforms any disk oriented system.

## Basic concepts
The core component is the `Engine`. The engine is 100% ACID, runs in-process and hosts a user defined data model. The data model can be domain specific or generic and is defined using plain old NET types. Persistence is based on snapshots and write-ahead command logging to the underlying storage.

![image](figure1.png)

### The Model
* is an instance of the user defined data model
* lives in RAM only
* _is_ the data
* is a projection of the entire sequence of commands applied to the initial model, usually empty.
* can only be accessed through the engine

### The Client
* has no direct reference to the model
* interacts directly with the Engine either in-process or remote
* or indirectly via a proxy with the same interface as the model
* passes query and command objects to the engine


### The Engine
The Engine encapsulates an instance of the model and is responsible for atomicity, consistency, isolation and durability. It performs the following tasks:
* writes commands to the journal
* executes commands and queries
* reads and writes snapshots
* restores the model on startup

### The Command Journal
The journal is a complete log of every single command executed. The engine appends commands
*

## We call it a toolkit because you have a lot of options
* Modeling - define your own model or use an existing one. Generic or domain specific. It's up to you.
* Storage - File system (default), relational databases, Event Store, MongoDB, RavenDB
* Data format - Choose wire and storage format by plugging in different IFormatter implementations. Binary, JSON, ProtoBuf, etc
* Isolation - Serialized transactions, Non-blocking MVCC concurrency using Immutability kernel and model or manage concurrency yourself.

Read more in the docs on [Extensibility](/docs/extensibility)
