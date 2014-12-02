---
title: OrigoDB from an RDBMS perspective
layout: layout
comments: true
excerpt: In this article I will attempt to introduce OrigoDB from an RDBMS perspective, pointing out similarities and differences.
---

# {{page.title}}

In this article I will attempt to introduce OrigoDB from an RDBMS perspective, pointing out similarities and differences.

## The relational model
In a relational database data is stored in tables. Tables have rows and columns. Columns have datatypes. There are foreign keys and other constraints. This is called the relational model, one of many possible data models. Relational databases (and most other databases) support a single data model. OrigoDB has no single data model, either you create a domain specific model using NET types and collections or choose from a number of generic models: relational, document, key/value or graph.

## The data
SQL Server has one or more datafiles containing data. The data files are composed of 8kb blocks called data pages. Data is represented as b-trees or heaps and mapped onto  data pages. The buffer manager is the component responsible for loading blocks into memory (the buffer pool) and writing dirty pages back to disk during checkpoints.

OrigoDB on the other hand has a single object graph which resides entirely in memory. Transactions are applied directly to the in-memory graph. The data is not mapped to disk in any way. There is no concept of dirty data that needs to be written to disk. OrigoDB does however support snapshots. Taking a snapshot involves writing the entire in-memory graph to storage. Depending on size, snapshots can take from seconds to tens of minutes to perfom during which updates (but not reads) to the graph are blocked.  The main purpose of snapshots is to be able to load faster during system startup, not persistence.

## The transaction log
For each transaction, the RDBMS writes new, modified, deleted and original data pages to the transaction log. The log needs to contain enough information to be able to rollback the transaction, but also to recover after an unclean shutdown. In OrigoDB  the command journal corresponds to the transaction log. The primary purpose of the journal is to persist commands needed to rebuild the in-memory graph. On startup, OrigoDB loads the most recent snapshot and then replays the subsequent commands.

Commands are written to the journal using write-ahead logging. This is sometimes referred to as intent logging, in contrast to the traditional RDBMS which uses effect logging. By traditional RDBMS I mean the ones based on disk-centric b-tree architectures. VoltDB, a distributed rdbms redesigned from scratch to run entirely in-memory, uses intent logging.

## Truncating the log?
When using SQL Servers simple recovery model, the log is automatically truncated after each checkpoint. In full recovery, the log is truncated manually or automatically during log backup.

With intent logging, the cause of the change is logged, not the resulting changes. One consequence of this is that log entries are fairly small, less than 100 bytes per entry is common. Given the small size, we can often afford to keep the entire journal around forever with the benefit of having an complete history of every change, including the user who made the change. Replaying the whole journal is often faster than loading a snapshot. Another huge benefit is that we can redefine how commands are interpreted and generate a different (slightly or entirely) in-memory model.


## OrigoDB Commands
OrigoDB commands correspond roughly to stored procedures. Commands are the only mechanism through which data can be modified. Ad-hoc inserts, updates, deletes or any other modifications are not possible. Commands are written using any NET language.

## OrigoDB Queries
An OrigoDB query corresponds roughly to an RDBMS view, function or stored procedure given that no data is modified. Think of them as parameterized precompiled statements, written with any NET language and often using Linq.

## Transactions
Command and Query objects are the transactional units of OrigoDB.  Multiple commands can easily be grouped into a single composite command. Explicit transactions with BEGIN TRAN, COMMIT TRAN and ROLLBACK TRAN are not necessary.

## Rollback
OrigoDB does however have an automatic rollback function. If a command fails unexpectedly, we cannot be sure that the in-memory model has not been corrupted. In this case a full restore to the state prior to the failed command is performed which is an expensive operation! Fortunately, rollbacks can only be triggered by programming errors or system exceptions (OutOfMemory for example), never to reconcile two conflicting concurrent transactions, for instance deadlocks.

## Isolation levels and consistency
OrigoDB transactions are serialized. They execute one at a time providing perfect isolation and consistency. The default isolation level of SQL Server, and many other rdbms's,  is read committed.  This is a poor default in my opinion, sacrificing consistency and isolation in exchange for concurrency.


## Hosting
You can host an OrigoDB database within almost any NET/Mono web, desktop, windows service or console application using the open source embedded engine. Running OrigoDB embedded is similar to using SQLite or SQL Server Compact.

OrigoDB Server is a commercial dedicated host which can run as a service or from the command line. There are two protocols for communication with the server, native TCP or JSON over HTTP. Also there is a web based ui for basic administration and ad-hoc querying using LINQ or javascript.

## High Availability
With OrigoDB Server you can set up the equivalent of Sql Server database mirroring. Commands are handled at master (principal) and queries can be handled by the master or any mirror. The master replicates commands to each mirror (synchronously or asynchronously). Automatic failover is not yet supported.
