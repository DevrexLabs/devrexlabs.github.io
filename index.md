---
title: page title from page yml header
layout: layout.html
---

# What is OrigoDB?
OrigoDB is an in-memory database platform. The engine is 100% ACID, runs in-process and hosts a user defined data model. The data model can be domain specific or generic and is defined using plain old NET types. Persistence is achieved snapshots and write-ahead command logging to the underlying storage.

Our design goals focus around rapid development, testability, simplicity, correctness, modularity, flexibility and extensibility. Performance was never a goal but OrigoDB easily outperforms disk oriented systems. Yet there is room for significant performance optimizations.
 
 Title: {{page.title}}
 
## Example using a proxy
This is based on the twitter clone example in the http://github.com/devrexlabs/OrigoDb.Examples repository.
```
install-package origodb.core
```

```csharp
   var db = Db.For<TwitterVerse>("mode=embedded");
   db.AddUser("beavis");
   var id = db.Tweet("beavis", "I need TP for my bunghole", DateTime.Now);
   db.Favorite("beavis", id);
   db.Follow("beavis", "butthead");
   db.Favorite("butthead", id);
```
The method calls are transparently intercepted by the proxy, mapped to command objects
and persisted to the command journal.

## Resources
* Core documentation - http://github.com/devrexlabs/origodb/wiki/documentation
* http://origodb.com/ - Commercial site offering OrigoDB Server including enterprise features and support.
* http://geekstream.devrexlabs.com - tech blog search demo built with OrigoDB
* http://livedb.devrex.se/ - Old blog
* http://robertfriberg.se/ - New blog
* http://twitter.com/devrexlabs - announcements
* http://twitter.com/robertfriberg - rants
* @devrexlabs - Github organisation page
* http://eepurl.com/OGGHz - monthly newsletter
* http://groups.google.com/origodb - The mailing list for discussions and support
* http://www.nuget.org/packages/OrigoDB.Core/
* http://nuget.org/packages?q=origodb&sortOrder=package-download-count - Nuget packages
* todo - Binary downloads
