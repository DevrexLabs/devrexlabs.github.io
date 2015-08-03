---
title: FAQ
layout: layout
---

## {{page.title}}


### Is it really a database?
OrigoDB has user defined schemas, user defined commands and queries (corresponds to stored procedures and views), a query language (LINQ), fully ACID transactions, handles multiple concurrent clients and supports language independent protocols.

On the other hand, the schema can only be modified by recompiling the model, security features are limited and there are no ad-hoc write operations, only precompiled commands.

### Is it a graph database?
Sort of, yes. The user defined in-memory model is a strongly typed object graph. Every object in the graph is a node while every object property referencing an object is a relationship.
Commands manipulate the graph, queries traverse it.

To use a generic graph model, include [QuickGraph](https://quickgraph.codeplex.com) objects in the user defined OrigoDB model.

### How do I manage transactions?
You don't. There are no explicit transaction primitives like BEGIN, COMMIT and ROLLBACK. Instead each user defined command is an implicit transaction. If you need to perform multiple commands as a single transaction use the composite design pattern.

### How can it be persistent if it's in-memory?
Persistence is configurable. The default `PersistenceMode` is `Journaling` in which every command is written to the command journal. When the system loads, the commands are replayed and the in-memory state is restored.

### What are the other modes of persistence?
* `ManualSnapshots` - nothing is persisted unless you explicitly call Engine.CreateSnapshot()
* `SnapshotPerTransaction` - Take a snapshot after each successful command

### How long does it take to load a model?
It depends on the size and complexity of the snapshot, the number of commands replayed, storage i/o performance and the serializer used. A few seconds per GB of journal is considered normal.  

### How does OrigoDB relate to event sourcing?
Think of the in-memory model as a single event sourced aggregate. The commands in the journal represent the sequence of events leading to the current state of the model. The engine takes care of reading and writing to the journal, it is entirely transparent.

### How does OrigoDB relate to CQRS?
CQRS is an architectural pattern based on separate read and write models. You can use OrigoDB for the write model, read model or both.

Don't confuse OrigoDB commands with CQRS commands, the latter operate at a higher level while the former operate on the in-memory data model only.

### How does OrigoDB relate to Redis?
OrigoDB and Redis are very similar. Both keep the entire data model in memory and provide optional persistence based on journaling and snapshots.

Redis is a key value store where the keys are string and the values can be either simple scalars or any one of a predefined number of data structures. OrigoDB data models and operations are user defined.

Redis is written in C and optimized for performance. OrigoDB is written in C# and optimized for simplicity and developer productivity.

Redis runs in it's own process and accepts text based commands over a tcp connection. OrigoDB Server accepts encoded command and query objects over tcp or http.
