---
title: System prevalence in the ddd/cqrs forum
layout: layout
comments: true
excerpt: System prevalence has a lot in common with event sourcing and cqrs. I'm not sure about how aware the two communities are of each other so I posted the following to the ddd/cqrs google group.
---

# {{page.title}}
System prevalence has a lot in common with event sourcing and cqrs. I'm not sure about how aware the two communities are of each
other so I posted the following to the ddd/cqrs google group.
Here's a <a href="http://bit.ly/TWU3hx">link to the post</a>, in case you
want to follow the discussion.


> Howdy folks,
> Here's a short presentation of System Prevalence, a concept you should definitely be familiar with.
> What if you had enough RAM to hold ALL of your aggregates in memory? According to the prevalent hypothesis you do!
> A prevalence engine ...

> * maintains a single aggregate (the prevalent system) in memory
> * executes queries and command against the aggregate
> * writes commands to the command journal
> * reloads the aggregate by replaying commands from the journal on startup

> There are a few very significant things implied here regarding concurrency and transactions:

> * There is no separate database where your "real" data lives, the aggregate in memory IS the data. This means no concurrency/consistency conflicts
> * The entire system is a single aggregate and therefore also a single transactional boundary.
> * In memory is so fast that we can get away with an exclusive lock on the entire system for each command.
> * Full ACID support

> As you can see System Prevalence has some similarities with CQRS and Event Sourcing. Generally, there are fewer collaborating types
in a prevalent system architecture:

> * Engine
> * Commands
> * Queries
> * the prevalent system (aggregate, remember?)

> Notably, there are no command handlers or events. Of the CQRS/ES examples I've seen there is a lot of non-transparent complex event plumbing going on in the aggregates. Domain modeling for a prevalent system, on the other hand, is about as transparent as it gets.
> Prevayler (java), Madeleine (ruby), LiveDb (.NET) are a few of the many system prevalence implementations out there. (I'm the lead dev of LiveDB) Here are some links for additional reading:

> * An introduction to object prevalence (2002!) <a href="http://www.ibm.com/developerworks/library/wa-objprev/">http://www.ibm.com/developerworks/library/wa-objprev/</a>
> * System prevalence on wikipedia <a href="http://en.wikipedia.org/wiki/System_Prevalence">http://en.wikipedia.org/wiki/System_Prevalence</a>
> * Page on Ward Cunninghams wiki (a must read!) <a href="http://c2.com/cgi/wiki?ThePrevayler">http://c2.com/cgi/wiki?ThePrevayler</a>
> * Prevayler - one of the first implemantations by visionary Klaus Wuestefeld <a href="http://www.prevayler.org">www.prevayler.org</a>
> * The old prevayler wiki <a href="http://prevayler.org/old_wiki/Welcome.html">http://prevayler.org/old_wiki/Welcome.html</a>
> * LiveDB open source engine <a href="http://livedomain.codeplex.com">http://livedomain.codeplex.com</a>
> * Martin Fowler on LMAX<br /><a href="http://martinfowler.com/articles/lmax.html">http://martinfowler.com/articles/lmax.html</a>&nbsp;</p>
> * Hacker news comments on fowlers LMAX article <a href="http://news.ycombinator.com/item?id=3173993">http://news.ycombinator.com/item?id=3173993</a>
> * Martin Fowler calls it Memory Image <a href="http://martinfowler.com/bliki/MemoryImage.html">http://martinfowler.com/bliki/MemoryImage.html</a>
> * What Klaus thinks the name MemoryImage <a href="https://gist.github.com/1186975">https://gist.github.com/1186975</a>
> * Geekstream - LiveDB server example app <a href="http://geekstream.devrex.se">http://geekstream.devrex.se</a>
> * Geekstream source on github<a href="http://github.org/devrexlabs/geekstream">http://github.org/devrexlabs/geekstream</a>
> * Prevayler related stuff on Hacker News <a href="http://metaoptimize.com/projects/autotag/hackernews/term/88/prevayler.html">http://metaoptimize.com/projects/autotag/hackernews/term/88/prevayler.html</a>
