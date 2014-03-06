---
title: Start
layout: layout
---

Hey! We're building OrigoDB, the open source in-memory database toolkit for NET/Mono. You should probably try it out!

## What is OrigoDB?
OrigoDB is an in-memory database platform. The engine is 100% ACID, runs in-process and hosts a user defined data model.
The data model can be domain specific or generic and is defined using plain old NET types.
Persistence is achieved by snapshots and write-ahead command logging to the underlying storage.

Our design goals focus around rapid development, testability, simplicity, correctness,
modularity, flexibility and extensibility. Performance was never a goal but OrigoDB easily 
outperforms disk oriented systems. So there is room for performance optimizations.

 
## Example user defined model
{%highlight csharp%}
[Serializable]
public class KeyValueStore : OrigoDB.Core.Model
{
   Dictionary<string,object> _store = new Dictionary<string,object>();
   
   public void Put(string key, object value)
   {
      _store[key] = value;
   }
   
   public void Remove(string key)
   {
      _store.Delete(key);
   }
}
{% endhighlight %}
## Sample usage using proxy
{%highlight csharp%}
   var db = Db.For<KeyValueStore>("mode=embedded");
   db.Put("key", someValue);
{% endhighlight %}

The method calls are transparently intercepted by the proxy, mapped to command objects
and persisted to the command journal.
@devrexlabs - Github organisation page
## Resources
* Core documentation - http://github.com/devrexlabs/origodb/wiki/documentation
* [http://origodb.com/] - Commercial site offering OrigoDB Server including enterprise features and support.
* http://geekstream.devrexlabs.com - tech blog search demo built with OrigoDB
* http://livedb.devrex.se/ - Old blog
* http://robertfriberg.se/ - New blog
* http://twitter.com/devrexlabs - announcements
* http://twitter.com/robertfriberg - rants
 
* http://eepurl.com/OGGHz - monthly newsletter
* http://groups.google.com/origodb - The mailing list for discussions and support
* http://www.nuget.org/packages/OrigoDB.Core/
* http://nuget.org/packages?q=origodb&sortOrder=package-download-count - Nuget packages
* todo - Binary downloads
