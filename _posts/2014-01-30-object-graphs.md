---
title: Understanding object graphs
layout: layout
comments: true
excerpt: Do you remember the days when file formats had to be hand crafted not to mention all </span>the code needed to read and write them? Or even more cumbersome, implementing a network communication protocol? In some cases you probably still need to but fortunately there is a simpler way. Most platforms support reading and writing objects to a stream, file or string taking care of all the gory details for you. I first discovered object serialization when learning Java. I was immediately impressed by the power and simplicity. Days of work could now be done in seconds using a few lines of code.
---

# {{page.title}}
<span style="line-height: 1.5em;">Do you remember the days when file formats had to be hand crafted not to mention all </span>the code needed to read and write them? Or even more cumbersome, implementing a network communication protocol? In some cases you probably still need to but fortunately there is a simpler way. Most platforms support reading and writing objects to a stream, file or string taking care of all the gory details for you.

I first discovered object serialization when learning Java. I was immediately impressed by the power and simplicity. Days of work could now be done in seconds using a few lines of code.

Here's some code in C# to show how simple it is:

{% gist rofr/8721330 %}

<span style="line-height: 1.5em;">The stream abstraction means you can target files, memory or sockets. It also allows supports </span>the decorator pattern to transparently add things like encryption and compression. Powerful!

Notice that the first argument to WriteToStream is named graph. The reason is to be explicit about what gets written to the Stream. The state of an object is defined by the current value of each of it's fields. But what if a field is a reference to some other object? There you have it. A graph. And what if there's a reference to a collection like a list or dictionary? Same thing, the collection is an object containing references to it's items. It's all a graph and it can get very big and complex.

The BinaryFormatter will walk the entire graph, writing each object to the stream while keeping track of multiple references to the same object. When reading it back an identical graph is constructed.

Keep in mind that references are one-way. If we have this graph structure: A -&gt; B -&gt; C and we pass B to WriteToStream, only B and C are written to the stream.

So why am I writing this? Most developers understand serialization, what's the big deal? I'm trying to paint a bigger picture here and this is a detail that plays in. OrigoDB makes heavy use of serialization. Snapshots, command journalling, command replication and cloning result graphs to name a few. But those are just technical details and not really significant. The key point here is that an OrigoDB in-memory database is just a plain old object graph. We'll dig deeper into this fact in a coming post. Stay tuned!
