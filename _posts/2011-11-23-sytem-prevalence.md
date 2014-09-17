---
title: System prevalence
layout: layout
comments: true
---

# {{page.title}}
Just posted the following in the Design Patterns group on LinkedIn:
 
> Anyone familiar with this architectural pattern?
 
> It is a persistence mechanism similar to event sourcing, command sourcing would be a more accurate term. A prevalence engine holds the *system* in memory and provides synchronized access to queries and commands. When a command has executed it is written to a durable command journal. On startup the system is rebuilt by loading a snapshot (if one exists) and then replaying a sequence of commands.
 
> The *system* can be just about anything represented natively on the platform used. It could be an object-oriented domain model or a 12 dimensional bit-array, providing a great deal of flexibility. It's possible to do relational, graph or document modeling using any high level language and therefore a candidate architecture for building in-memory databases given that you can represent modification as discrete, deterministic, serializable commands.